# Game #2 — Effect Model

**Status:** 🟡 Working Design
**Version:** 0.1

---

## 1. Purpose

The Effect System defines the actual gameplay changes produced by components.

A component does not directly modify the player.

Instead:

> **Component → Effects → Target → Gameplay Result**

This separation allows the same effect to be reused in different situations.

---

## 2. Effect Structure

Every effect conceptually contains:

```text id="q2k7nd"
Effect
├── Type
├── Target
├── Value
├── Duration
├── Trigger
├── Conditions
├── Tags
├── Stacking Rule
└── Modifier Data
```

Not every effect needs every field.

---

## 3. Effect Type

The Effect Type describes **what the effect does**.

Initial effect families:

### Damage

* Physical Damage
* Fire Damage
* Lightning Damage
* Poison Damage
* Shadow Damage
* Future damage types

### Defense

* Armor
* Shield
* Resistance
* Damage Reflection

### Status

* Burn
* Poison
* Shock
* Slow
* Bleed
* Stun

### Movement

* Movement Speed
* Dash
* Jump Enhancement
* Flight
* Teleport

### Recovery

* Healing
* Regeneration
* Lifesteal

### Utility

* Detection
* Knockback
* Pull
* Resource Generation
* Other utility effects

New effect types can be added later.

---

## 4. Target

The Target determines **what gameplay system receives the effect**.

Initial target categories:

* Player
* Attack
* Ability
* Defense
* Movement
* Projectile
* Enemy
* Environment

The Target Model will be designed separately in greater detail.

---

## 5. Value

An effect can have one or more values controlling its strength.

Examples:

```text id="7ecf8b"
Fire Damage
Value: 20
```

or:

```text id="4mg4ot"
Movement Speed
Value: +15%
```

or:

```text id="2kg9jb"
Burn
Damage: 5 / second
Duration: 4 seconds
```

The exact value representation will be finalized during implementation.

---

## 6. Duration

Effects can have different lifetimes.

### Instant

Occurs once.

Example:

> Explosion damage.

### Temporary

Exists for a specific duration.

Example:

> Burn for 5 seconds.

### Permanent

Exists while the relevant component/build is active.

Example:

> +10% armor while equipped.

### Conditional

Exists or activates only while a condition is satisfied.

Example:

> +20% damage while below 30% health.

---

## 7. Trigger

The Trigger determines **when an effect activates**.

Initial triggers may include:

* On Equip
* On Attack
* On Hit
* On Critical Hit
* On Taking Damage
* On Dodge
* On Dash
* On Kill
* On Low Health
* On Death
* Periodic
* On Enter Area
* On Exit Area

New triggers can be introduced when required by gameplay.

---

## 8. Conditions

Conditions restrict when an effect is allowed to activate.

Examples:

```text id="1m49tb"
Trigger:
  On Hit

Condition:
  Target is Burning

Effect:
  Lightning Damage +50%
```

Another example:

```text id="ckq5m7"
Condition:
  Player Health < 30%

Effect:
  Regeneration
```

Conditions should be reusable across different effects.

---

## 9. Tags

Effects contain tags that allow other systems to reason about them.

Example:

```text id="1v6t4k"
Fire Damage

Tags:
  FIRE
  ELEMENTAL
  DAMAGE
  OFFENSIVE
```

Another:

```text id="ck2e0c"
Armor

Tags:
  DEFENSIVE
  PROTECTION
```

Tags are used by the Combination/Synergy Engine and other gameplay systems.

---

## 10. Stacking Rules

When multiple instances of an effect are active, the system needs to know how they interact.

Supported stacking behaviors may include:

### Additive

Values are added.

Example:

> +20% + +20% = +40%

### Multiplicative

Effects multiply each other.

Example:

> 1.20 × 1.20 = 1.44

### Highest Only

Only the strongest instance applies.

### Refresh

A new application refreshes the duration.

### Independent

Multiple instances coexist independently.

The stacking rule should be defined per effect where necessary.

---

## 11. Effect Modifiers

An effect may modify another effect.

Example:

```text id="v3v8t5"
Fire Core
→ Fire Damage
```

Another component might provide:

```text id="0k7bsh"
Effect:
  Increase Elemental Damage
Value:
  +20%
```

The system can therefore produce:

> Fire Damage +20%

without creating a new "Strong Fire Core" component.

Modifiers can affect properties such as:

* Damage
* Duration
* Range
* Area
* Cooldown
* Speed
* Frequency
* Chance
* Quantity

---

## 12. Effects Can Be Layered

A final gameplay action can contain multiple effects.

Example:

```text id="j45u3d"
Player Attack
│
├── Physical Damage
├── Fire Damage
├── Burn
└── Knockback
```

Another build could produce:

```text id="3r8g5n"
Player Attack
│
├── Physical Damage
├── Lightning Damage
├── Shock
└── Chain Effect
```

The system does not need separate attack implementations for each build.

The attack is assembled from its active effects.

---

## 13. Effects Can Modify Other Effects

This is a fundamental capability.

For example:

```text id="uh4qwc"
Fire
    ↓
modifies
    ↓
Attack
    ↓
adds
    ↓
Fire Damage
```

Another component can modify the Fire Damage effect:

```text id="h7svl3"
Increase Fire Damage
    ↓
modifies
    ↓
Fire Damage
```

This allows effects to form layers rather than requiring hardcoded combinations.

---

## 14. Effect Resolution

When a build changes, effects should be resolved through a predictable process:

```text id="9ujvgo"
Active Components
       ↓
Extract Effects
       ↓
Validate Targets
       ↓
Apply Modifiers
       ↓
Resolve Interactions
       ↓
Resolve Synergies
       ↓
Resolve Conflicts
       ↓
Final Active Effects
```

The exact order of these stages will be finalized when the Combination/Synergy Engine is designed.

---

## 15. Example

Player equips:

**Claw + Fire Core + Lightning Core**

Components provide:

```text id="2m9gbl"
Claw
→ Melee Attack

Fire Core
→ Fire Damage

Lightning Core
→ Lightning Damage
```

The system determines that both elemental effects can target the melee attack.

Final attack:

```text id="3r8h7a"
Melee Attack
├── Physical Damage
├── Fire Damage
└── Lightning Damage
```

The Synergy Engine may then detect:

```text
FIRE + LIGHTNING
```

and produce an additional effect such as:

```text
OVERLOAD
```

No specific "Fire + Lightning + Claw" build needs to be manually created.

---

## 16. Design Principle

The Effect System should answer:

> **What happens?**

The Component System answers:

> **What provides it?**

The Target System answers:

> **What does it affect?**

The Combination/Synergy System answers:

> **How do multiple effects interact?**

Keeping these responsibilities separate is essential for scalability.

---

## 17. Current Open Questions

* Exact Target Model
* Exact effect data types
* Exact modifier model
* Effect priority/order
* Conflict resolution
* Synergy detection
* Synergy output
* Whether effects can create other effects
* Limits on effect chains
* How effects interact with environmental objects
