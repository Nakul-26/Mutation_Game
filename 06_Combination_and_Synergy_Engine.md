# Game #2 — Combination & Synergy Engine

**Status:** 🟡 Working Design
**Version:** 0.1

---

## 1. Purpose

The Combination & Synergy Engine determines how multiple component effects interact.

Its primary goal is:

> **Create complex and meaningful builds from a small number of reusable components without manually designing every possible combination.**

The engine should favor **generic rules** over individual combination definitions.

---

# 2. Two Levels of Interaction

The system has two distinct interaction mechanisms:

### Generic Interactions

Rules that work broadly across many components.

Example:

> An elemental effect attached to a compatible attack adds that element's damage to the attack.

This automatically allows:

* Fire + Claw
* Fire + Blade
* Lightning + Claw
* Lightning + Projectile
* etc.

### Special Synergies

Specific interactions between properties/tags that produce an additional effect.

Example:

> Fire + Lightning → Overload

These should be relatively limited and meaningful.

---

# 3. Components Do Not Know About Each Other

A component should not contain logic such as:

```text
If Fire is equipped:
    do X
```

Instead, components expose:

* Effects
* Tags
* Properties
* Compatibility information

The engine evaluates those properties.

This keeps components independent.

---

# 4. Interaction Pipeline

The engine operates after the player's components have been collected.

```text id="5v0q7m"
Active Components
       ↓
Extract Effects
       ↓
Group Effects by Target
       ↓
Apply Generic Rules
       ↓
Detect Synergy Conditions
       ↓
Generate Synergy Effects
       ↓
Resolve Newly Generated Effects
       ↓
Resolve Conflicts
       ↓
Final Build
```

The exact recursion/chain rules will be defined during implementation.

---

# 5. Tags Are the Primary Connection Mechanism

Components and effects use tags to describe their properties.

Example:

```text id="5k7f2m"
Fire Core

Tags:
  FIRE
  ELEMENTAL
  DAMAGE
```

Claw:

```text id="0v7s3a"
Tags:
  MELEE
  WEAPON
  PHYSICAL
```

Lightning:

```text id="r3k9x1"
Tags:
  LIGHTNING
  ELEMENTAL
  DAMAGE
```

Rules can operate on these tags.

---

# 6. Generic Rule Example

Suppose the engine has this generic rule:

> **ELEMENTAL + ATTACK → Add elemental damage to the attack.**

The player equips:

```text id="5v4s2x"
Claw
Fire
```

The engine sees:

```text
Claw
→ MELEE / WEAPON

Fire
→ FIRE / ELEMENTAL / DAMAGE
```

The generic rule applies:

```text
Attack
+
Fire Damage
```

Result:

> Fire-enhanced melee attack.

No Claw-specific Fire rule exists.

---

# 7. Another Generic Rule

Suppose:

> **ELEMENTAL + DEFENSE → Add elemental retaliation to Defense.**

Player equips:

```text
Armor
Lightning
```

Result:

> When the player takes damage, the attacker receives Lightning damage.

Again, the Lightning component does not need to know about Armor.

---

# 8. Synergy Rules

Synergies are additional rules triggered when particular properties coexist.

Example:

```text id="m2x9q7"
Condition:
  FIRE tag present
  LIGHTNING tag present

Result:
  Add OVERLOAD effect
```

The result might be:

> Lightning and Fire attacks have a chance to trigger an electrical explosion.

The engine does not care whether the player obtained Fire from:

* a magical artifact
* an alien organ
* experimental technology

Only the relevant properties matter.

---

# 9. Synergies Should Produce Effects, Not New Components

Avoid:

```text
Fire + Lightning
→ Create "Electro-Flame Component"
```

Instead:

```text
Fire + Lightning
→ Generate Overload Effect
```

The resulting effect becomes part of the player's current Build State.

This keeps the number of components small.

---

# 10. Synergies Can Be Directional

Some interactions may depend on a relationship between effects.

Example:

```text
Fire
+
Poison
```

could produce:

> Toxic Flame

while:

```text
Poison
+
Fire
```

should normally produce the same result.

Unless a future mechanic explicitly introduces ordering, component order must not matter.

---

# 11. Synergies Can Use Multiple Tags

A synergy should not necessarily require exact component identities.

Example:

```text
Required Tags:
  FIRE
  DAMAGE
  MELEE
```

This could trigger regardless of whether the melee source is:

* Claw
* Blade
* Tentacle
* Future biological weapon

This is significantly more scalable than:

```text
Claw + Fire → ...
Blade + Fire → ...
Tentacle + Fire → ...
```

---

# 12. Synergy Specificity

Synergies can exist at different levels.

### Broad

```text
FIRE + LIGHTNING
→ OVERLOAD
```

### More specific

```text
FIRE + LIGHTNING + PROJECTILE
→ Explosive Lightning Projectile
```

### Highly specific

```text
FIRE + LIGHTNING + MELEE
→ Electrified Flame Strike
```

We should favor broad rules wherever possible.

Highly specific rules should be rare and reserved for particularly interesting interactions.

---

# 13. Synergy Priority

If multiple synergies are possible, the engine needs deterministic behavior.

Each synergy can eventually have:

```text
Priority
```

Example:

```text
Basic Interaction: Priority 10
Special Synergy: Priority 50
Major Synergy: Priority 100
```

Higher-priority interactions can be evaluated later in the resolution pipeline.

The exact priority system will be finalized after prototype testing.

---

# 14. Avoiding Combination Explosion

The system must never require:

```text
12 components
→ hundreds of pair rules
→ thousands of triple rules
→ thousands of four-component rules
```

Instead, we want:

```text
Components
   ↓
Reusable Tags
   ↓
Reusable Effects
   ↓
Generic Rules
   ↓
Small Synergy Library
```

Adding a new component should mostly involve defining:

> What properties and effects does this component provide?

The existing engine should handle most interactions automatically.

---

# 15. Example With Four Components

Player equips:

```text id="1x5g8k"
Claw
Fire
Lightning
Dash
```

### Component effects

```text
Claw
→ Melee Attack

Fire
→ Fire Damage

Lightning
→ Lightning Damage

Dash
→ Dash
```

### Generic resolution

```text
Claw
+ Fire
→ Fire Melee

Claw
+ Lightning
→ Lightning Melee

Dash
→ Movement Ability
```

### Synergy detection

```text
Fire + Lightning
→ Overload
```

### Final Build

```text
Attack
├── Physical Damage
├── Fire Damage
├── Lightning Damage
└── Overload

Movement
└── Dash
```

The engine never needed a predefined:

> "Claw + Fire + Lightning + Dash" build.

---

# 16. Multiple Components With the Same Property

Suppose the player eventually has:

```text
Fire Core
Fire Weapon
```

Both provide FIRE.

The system should treat:

```text
FIRE
```

as a property that can have multiple sources.

The exact numerical stacking behavior belongs to the Effect Model.

The Synergy Engine should primarily determine:

> **What properties are present?**

rather than directly handling every numerical calculation.

---

# 17. Synergy Output Can Feed Back Into the System

A synergy may create a new effect.

Example:

```text
Fire + Lightning
→ Overload
```

Overload may itself have:

```text
Tags:
  ELECTRIC
  EXPLOSION
  AREA_DAMAGE
```

These tags can potentially participate in later generic rules.

However, effect chains must have limits to prevent:

> Effect → Synergy → Effect → Synergy → infinite loop.

The implementation must therefore include effect-chain protection.

---

# 18. Build Independence

The engine should produce the same result regardless of the order in which components are equipped.

For example:

```text
Claw → Fire → Lightning
```

and:

```text
Lightning → Claw → Fire
```

must resolve to the same build.

Unless a future mechanic explicitly introduces order-dependent components.

---

# 19. Testing Strategy

The first prototype will contain **3–4 components**.

If we begin with four components, we have:

### 1-component builds

4

### 2-component builds

6

### 3-component builds

4

### 4-component builds

1

### Total

**15 non-empty combinations**

We should test **all 15 combinations** manually.

Additionally, we should test:

* Adding components
* Removing components
* Replacing components
* Re-equipping in different orders
* Conflicting effects
* Duplicate properties
* Synergy activation
* Synergy removal
* Multiple synergies
* Effect chains

---

# 20. Success Criteria

The prototype succeeds if:

### A. Components remain independent.

### B. Most interactions are generated by generic rules.

### C. Only a small number of explicit synergy rules are required.

### D. Removing a component correctly removes its effects.

### E. Adding a component dynamically updates the build.

### F. Component order does not accidentally change the result.

### G. Different builds produce meaningfully different gameplay.

### H. Adding a fifth component does not require redesigning the entire system.

---

# 21. Design Principle

The fundamental principle is:

> **We design rules, not combinations.**

We should spend our development effort creating:

* Good components
* Good effects
* Good tags
* Good generic rules
* A small number of excellent synergies

rather than manually designing every possible build.

---

## 22. Current Architecture

```text
COMPONENTS
    ↓
EFFECTS + TAGS
    ↓
TARGET RESOLUTION
    ↓
GENERIC INTERACTION RULES
    ↓
SYNERGY DETECTION
    ↓
SYNERGY EFFECTS
    ↓
CONFLICT RESOLUTION
    ↓
FINAL BUILD STATE
```

---

## 23. Open Questions

* Exact rule data structure
* Exact synergy data structure
* How conditions are represented
* How priorities work
* How effect chains are limited
* How conflicting synergies are handled
* Whether synergies can disable other synergies
* Whether the player can see discovered synergies
* How hidden/undiscovered synergies are presented
* How many explicit synergies should exist per component
