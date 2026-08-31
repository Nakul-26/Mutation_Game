# Game #2 — First 4 Component Design

**Status:** 🟡 Prototype Candidates
**Version:** 0.1

---

# 1. Purpose

The first four components are not intended to represent the final game's complete component roster.

Their purpose is to **stress-test the component architecture**.

They must allow us to test:

* Basic offensive modification
* Elemental properties
* Defensive behavior
* Movement/ability modification
* Properties and traits
* Generic interactions
* Reactions
* Different component combinations

---

# 2. Component Selection

The first four prototype components are:

| Component        | Category              | Primary Role          |
| ---------------- | --------------------- | --------------------- |
| 🦾 Claw          | Biological            | Offensive             |
| 🔥 Fire Core     | Magical               | Elemental             |
| ⚡ Lightning Core | Experimental / Energy | Elemental             |
| 🪽 Wing Mutation | Biological            | Movement / Adaptation |

These are **prototype components**, not necessarily the final versions.

---

# 3. Component 1 — Claw

### Category

Biological Mutation

### Primary Role

Offensive

### Concept

The player grows a powerful biological claw that modifies the basic melee attack.

### Base Properties

```text
MELEE
BIOLOGICAL
PHYSICAL
WEAPON
```

### Base Effects

```text
Melee Attack
Physical Damage
```

### Expected Behavior

Without other components:

> Player performs a claw-based melee attack.

With Fire:

> Fire-enhanced melee attack.

With Lightning:

> Lightning-enhanced melee attack.

With Fire + Lightning:

> Melee attack containing both elemental effects and potentially an elemental reaction.

### Purpose in Prototype

Claw establishes the **base attack target** that other components can modify.

---

# 4. Component 2 — Fire Core

### Category

Magical Artifact / Material

### Primary Role

Elemental

### Base Properties

```text
FIRE
ELEMENTAL
HEAT
```

### Base Traits

```text
BURNING
```

### Base Effects

```text
Fire Damage
Burn
```

### Intended Target Compatibility

Potentially:

```text
ATTACK
ABILITY
PROJECTILE
DEFENSE
ENVIRONMENT
```

The first prototype does not need to implement every target.

### Expected Behavior

When applied to an attack:

> Attack deals Fire damage and can cause Burn.

When applied to an ability:

> Ability gains Fire behavior.

Future possibilities:

> Fire movement trail
> Fire defense
> Environmental ignition

### Purpose in Prototype

Fire tests:

* Properties
* Traits
* Elemental effects
* Target compatibility
* Modification of another component's behavior

---

# 5. Component 3 — Lightning Core

### Category

Experimental Technology / Energy

### Primary Role

Elemental

### Base Properties

```text
LIGHTNING
ELEMENTAL
ELECTRIC
```

### Base Traits

```text
CONDUCTIVE
CHAINING
```

### Base Effects

```text
Lightning Damage
Shock
```

### Intended Target Compatibility

Potentially:

```text
ATTACK
ABILITY
PROJECTILE
DEFENSE
ENVIRONMENT
```

Again, only the necessary targets need to be implemented initially.

### Expected Behavior

When applied to an attack:

> Attack deals Lightning damage and can cause Shock.

When applied to Defense:

> Damage received may trigger electrical retaliation.

### Purpose in Prototype

Lightning tests:

* A second elemental property
* Multiple effects on the same target
* Elemental interactions
* Defensive targeting
* Potential chain behavior

---

# 6. Component 4 — Wing Mutation

### Category

Biological Mutation

### Primary Role

Movement / Adaptation

### Base Properties

```text
BIOLOGICAL
AERIAL
MOBILITY
```

### Base Traits

```text
FLIGHT
```

### Base Effects

```text
Enhanced Jump / Air Movement
```

The exact movement mechanic will be determined during combat/movement implementation.

### Possible Future Interactions

With Fire:

> Fire trail during aerial movement.

With Lightning:

> Electrical aerial movement.

With other future components:

> Different movement modifications.

### Purpose in Prototype

Wing exists primarily to prove that the component system isn't just an **attack modifier system**.

It tests:

> Component → Movement

rather than:

> Component → Attack.

---

# 7. Why These Four?

These four deliberately create different system dimensions.

```text id="4z7m2x"
Claw
  ↓
Attack

Fire
  ↓
Element

Lightning
  ↓
Element

Wing
  ↓
Movement
```

This gives us:

### Claw + Fire

Tests:

> Basic effect modification.

### Claw + Lightning

Tests:

> Another elemental modification.

### Fire + Lightning

Tests:

> Property/reaction system.

### Claw + Fire + Lightning

Tests:

> Multiple effects + reaction.

### Wing + Fire

Tests:

> Element + movement.

### Wing + Lightning

Tests:

> Element + movement.

### Wing + Fire + Lightning

Tests:

> Multiple properties interacting outside the attack system.

---

# 8. Expected Build Examples

These are **expected outcomes**, not hardcoded recipes.

### Claw

```text
Melee Attack
```

### Claw + Fire

```text
Melee Attack
+ Fire
+ Burn
```

### Claw + Lightning

```text
Melee Attack
+ Lightning
+ Shock
```

### Claw + Fire + Lightning

```text
Melee Attack
+ Fire
+ Lightning
+ Possible Elemental Reaction
```

### Wing

```text
Enhanced Air Movement
```

### Wing + Fire

Potentially:

```text
Enhanced Air Movement
+ Fire-related movement effect
```

### Wing + Lightning

Potentially:

```text
Enhanced Air Movement
+ Lightning-related movement effect
```

The exact outcomes will be determined by the generic interaction and reaction systems.

---

# 9. Important Constraint

We should **not** guarantee that every pair creates a special ability.

For example:

```text
Claw + Fire
```

does not necessarily need a named:

> "Fire Claw"

ability.

It can simply be:

> Melee Attack + Fire Damage + Burn.

This is intentional.

Special reactions should be reserved for interactions that genuinely improve gameplay.

---

# 10. Prototype Component Data

Conceptually:

```text id="7x2m5q"
CLAW
Properties:
  MELEE
  BIOLOGICAL
  PHYSICAL
  WEAPON

FIRE CORE
Properties:
  FIRE
  ELEMENTAL
  HEAT

Traits:
  BURNING

LIGHTNING CORE
Properties:
  LIGHTNING
  ELEMENTAL
  ELECTRIC

Traits:
  CONDUCTIVE
  CHAINING

WING
Properties:
  BIOLOGICAL
  AERIAL
  MOBILITY

Traits:
  FLIGHT
```

These definitions are intentionally simple.

We should not add unnecessary properties until testing proves they are useful.

---

# 11. Prototype Development Order

We should implement them incrementally.

### Stage 1

**Claw**

Test:

> Basic component → Attack.

### Stage 2

**Fire**

Test:

> Component → Element → Attack.

### Stage 3

**Lightning**

Test:

> Multiple elemental effects.

### Stage 4

**Wing**

Test:

> Component → Movement.

At each stage, stop and test before continuing.

---

# 12. Important Testing Principle

We should not immediately implement every possible behavior described above.

For example, Fire may theoretically support:

```text
Attack
Defense
Ability
Projectile
Movement
Environment
```

But Prototype v0.1 should implement only the behaviors necessary to prove the architecture.

Additional behaviors can be added after the base system works.

---

# 13. Success Criteria

The four components are successful if they demonstrate:

### Claw

Basic attack modification works.

### Fire

An elemental component can modify another system.

### Lightning

Multiple elemental effects can coexist.

### Wing

A component can modify movement rather than attack.

### Combined

Multiple components can produce a meaningful build without manually coding each combination.

---

# 14. Important Open Question

The exact **slot assignment** for these components is intentionally not finalized here.

The prototype will first validate:

> **Component → Effect → Target → Interaction**

before introducing all the restrictions of the final six-slot loadout.

---

# 15. Core Principle

> **The first four components are test instruments for the architecture, not simply the first four pieces of content.**

If these four work well, they become the foundation for designing components 5–12.
