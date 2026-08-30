# Game #2 — Reaction System

**Status:** 🟡 Experimental Design
**Version:** 0.1

---

# 1. Purpose

The Reaction System determines what happens when **properties, traits, effects, and targets interact**.

The primary goal is:

> **Create interesting combinations from general rules rather than manually defining every pair of components.**

For example, we should prefer a system that understands:

> Heat + Toxic → combustion-like reaction

over manually defining:

> Fire + Poison → Toxic Flame.

---

# 2. Reaction vs Synergy

These are intentionally different concepts.

### Reaction

A reaction is generated from **properties/traits interacting under conditions**.

Example:

```text
HEAT + TOXIC
→ TOXIC_COMBUSTION
```

### Synergy

A deliberately designed interaction between specific gameplay properties.

Example:

```text
FIRE + LIGHTNING + MELEE
→ SPECIAL_ATTACK
```

Reactions should be the primary scalable mechanism.

Synergies should be reserved for special, deliberately designed gameplay moments.

---

# 3. Reaction Structure

A reaction conceptually contains:

```text id="2c7m8q"
Reaction
├── Required Properties
├── Required Traits
├── Target Requirements
├── Context Requirements
├── Thresholds
├── Result Effects
├── Priority
└── Cooldown / Activation Limit
```

The reaction system evaluates whether the requirements are satisfied.

---

# 4. Property-Based Reactions

Reactions should preferably operate on **properties**, not component identities.

Example:

```text id="4n7x2p"
Required:
  HEAT
  TOXIC

Result:
  TOXIC_COMBUSTION
```

Any future components that provide:

```text
HEAT
```

and:

```text
TOXIC
```

can potentially trigger the same reaction.

This means new components automatically inherit existing interactions.

---

# 5. Trait-Based Reactions

Reactions can also operate on traits.

Example:

```text id="9v3m6k"
Required:
  SPREADING
  BURNING

Result:
  FIRE_SPREAD
```

This could work regardless of which components created the traits.

---

# 6. Target Context

The same properties can behave differently depending on the target.

Example:

```text id="0w7x3q"
HEAT + TOXIC
Target: ENEMY
→ Toxic Combustion
```

But:

```text id="2r8m5c"
HEAT + TOXIC
Target: ENVIRONMENT
→ Toxic Gas / Hazard
```

Therefore, reactions can optionally specify target requirements.

---

# 7. Trigger Context

Reactions do not necessarily happen continuously.

Possible reaction triggers include:

* On Attack
* On Hit
* On Taking Damage
* On Ability Activation
* On Movement
* On Enter Environment
* On Contact
* Periodically

Example:

```text id="6k1p9x"
Condition:
  Fire effect hits poisoned enemy

Reaction:
  Toxic Combustion
```

---

# 8. Context Is Important

A reaction should not merely ask:

> "Are Fire and Poison present somewhere in the build?"

It should often ask:

> **"Are the relevant properties affecting the same target or event?"**

For example:

Having:

```text
Fire → Attack
Poison → Defense
```

should not automatically create:

> Toxic Flame Attack.

The properties are being used in different contexts.

This prevents nonsensical interactions.

---

# 9. Reaction Inputs

A reaction can use several types of inputs:

### Property

Example:

```text
FIRE
```

### Trait

Example:

```text
SPREADING
```

### Effect

Example:

```text
BURN
```

### Target

Example:

```text
ENEMY
```

### Event

Example:

```text
ON_HIT
```

### State

Example:

```text
TARGET_IS_WET
```

This allows reactions to become increasingly sophisticated without requiring new component logic.

---

# 10. Example: Fire + Poison

Suppose the player has:

```text id="4z7k1m"
Fire
→ HEAT
→ BURNING

Poison
→ TOXIC
→ CORROSIVE
```

Both affect the same enemy.

The reaction system evaluates:

```text id="8q2n6v"
HEAT
+
TOXIC
+
SAME TARGET
```

Reaction:

```text id="6x5m1c"
TOXIC_COMBUSTION
```

Result could include:

* Burst damage
* Burn
* Poison
* Area effect

The exact result is a design decision.

---

# 11. Example: Fire + Lightning

Suppose:

```text id="9m4p2v"
Fire
→ HEAT

Lightning
→ ELECTRIC
```

A reaction rule might detect:

```text id="3x8k6q"
HEAT
+
ELECTRIC
+
SAME TARGET
```

and produce:

> Electrical Overload

Again, the rule does not need to know that the sources were specifically "Fire Core" and "Lightning Core."

---

# 12. Reactions Should Be Sparse

We should **not** attempt to create a reaction for every possible property combination.

For example:

```text
Fire + Poison
Fire + Lightning
Fire + Shadow
Fire + Ice
Poison + Lightning
Poison + Shadow
...
```

This simply recreates the combination-explosion problem.

Instead, reactions should exist where they create meaningful gameplay.

Many properties can simply coexist.

---

# 13. Reaction Families

Instead of thinking about individual pairs, we can potentially define **reaction families**.

For example:

### Elemental Interaction

```text
Element + Element
```

The system determines whether a meaningful reaction exists based on their properties.

### Conductivity

```text
ELECTRIC + CONDUCTIVE
```

### Combustion

```text
HEAT + FLAMMABLE
```

### Corrosion

```text
CORROSIVE + MATERIAL
```

### Propagation

```text
SPREADING + EFFECT
```

These generic reaction families can potentially produce many behaviors.

---

# 14. Reaction Conditions

A reaction may require additional conditions.

Example:

```text id="3k9x7q"
Required:
  FIRE
  POISON

Additional condition:
  Target has at least 1 stack of Poison
```

Result:

```text id="7v4m2c"
Toxic Combustion
```

This allows reactions to depend on actual gameplay state rather than merely equipped components.

---

# 15. Reaction Magnitude

Reaction strength can depend on the input strengths.

Example:

```text id="9q2w6m"
Fire Strength: 40
Poison Strength: 30
```

Reaction strength might be derived from:

> Fire strength + Poison strength

or another formula.

This allows stronger components to produce stronger reactions without creating separate reaction types.

---

# 16. Reaction Chains

A reaction can potentially create another effect.

Example:

```text id="7c4m8p"
Fire
+
Poison
↓
Toxic Combustion
↓
Explosion
↓
Area Damage
```

However, reaction chains must be strictly controlled.

Potential safeguards:

* Maximum reaction depth
* Maximum reactions per event
* Prevent repeating the same reaction
* Event-level activation limits

---

# 17. Reaction Ownership

A reaction belongs to the **system**, not to a component.

For example:

```text id="9r3k6v"
Fire Core
```

does not contain:

> "If Poison exists, create Toxic Combustion."

Instead:

```text id="2m8x5q"
Reaction System
```

contains the general reaction definition.

This maintains component independence.

---

# 18. Adding Future Components

Suppose we add:

> Plasma Organ

and it provides:

```text
ELECTRIC
HEAT
ENERGY
```

Existing reactions can automatically apply.

For example:

```text
ELECTRIC + CONDUCTIVE
```

still works.

```text
HEAT + FLAMMABLE
```

still works.

We don't necessarily need to modify the existing reaction engine.

---

# 19. Important Limitation

We should recognize that **true emergent behavior has a trade-off**.

If we make the system completely generic, it may produce:

* boring results
* unintuitive interactions
* balance problems
* combinations that are technically valid but not fun

Therefore, the system should be **hybrid**:

```text
Generic Property Rules
        +
Generic Reaction Families
        +
Small Number of Authored Special Reactions
```

This gives us systemic depth while retaining designer control.

---

# 20. Example Architecture

```text id="5y7q2m"
COMPONENTS
    ↓
PROPERTIES / TRAITS
    ↓
EFFECTS
    ↓
TARGET + EVENT
    ↓
REACTION ENGINE
    │
    ├── Generic Property Rules
    ├── Reaction Families
    └── Special Reactions
    ↓
NEW EFFECTS
    ↓
FINAL BUILD / GAMEPLAY
```

---

# 21. Development Strategy

We should **not implement a huge Reaction System immediately**.

During the 3–4 component prototype:

1. Implement the basic property system.
2. Implement generic effect interactions.
3. Implement a very small reaction framework.
4. Test whether reactions actually make gameplay more interesting.
5. Add only a few reactions.
6. Observe whether players understand them.
7. Expand only if the system proves useful.

---

# 22. Success Criteria

The Reaction System is successful if:

### A. New components inherit existing reactions automatically.

### B. Most combinations do not require custom code.

### C. Reactions depend on properties/context rather than component IDs.

### D. Reactions can produce meaningful gameplay changes.

### E. The system does not generate excessive or confusing interactions.

### F. Reaction chains remain controllable.

### G. Adding components does not require rewriting the engine.

---

# 23. Core Principle

> **We should model interactions between properties and circumstances, not between item names.**

The desired result is:

```text
Component
   ↓
Properties / Traits
   ↓
Effects
   ↓
Context
   ↓
General Reaction
   ↓
Gameplay
```

rather than:

```text
Component A + Component B
        ↓
Handcrafted Combination C
```

---

# 24. Current Architecture

```text id="5m8x2q"
COMPONENT
    ↓
PROPERTY / TRAIT
    ↓
EFFECT
    ↓
TARGET + EVENT + STATE
    ↓
GENERIC RULES
    ↓
REACTION FAMILIES
    ↓
SPECIAL SYNERGIES
    ↓
FINAL BUILD
```

---

## 25. Open Questions

* Exact reaction-family definitions
* Whether Properties and Traits should remain separate
* How reaction compatibility is determined
* How reaction strength is calculated
* How reaction priority works
* How simultaneous reactions are resolved
* How reaction chains are limited
* How much of the system should be data-driven
* Whether reactions should be visible or hidden from the player
* How reactions should be communicated visually and through UI
* How reactions will be balanced
