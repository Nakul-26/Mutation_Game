# Game #2 — Target Model

**Status:** 🟡 Working Design
**Version:** 0.1

---

## 1. Purpose

The Target Model defines **where an Effect can be applied**.

The Effect Model answers:

> **What happens?**

The Target Model answers:

> **What does it happen to?**

---

## 2. Core Target Categories

We will initially support these targets:

```text id="q7n3xm"
Player
Attack
Defense
Ability
Movement
Projectile
Enemy
Environment
```

These are gameplay targets, not necessarily individual game objects.

---

## 3. Player

Represents the player's overall state.

Examples:

* Maximum Health
* Regeneration
* Energy Capacity
* Resistance
* Detection
* Passive abilities

Example:

```text id="h9xk21"
Regeneration
Target: Player
```

Result:

> Player regenerates health over time.

---

## 4. Attack

Represents the player's offensive attack system.

An attack can contain multiple effects.

Example:

```text id="4m8zqf"
Attack
├── Physical Damage
├── Fire Damage
└── Burn
```

Components can modify an attack by adding:

* Damage types
* Status effects
* Range
* Attack speed
* Area
* Knockback
* Critical effects
* Elemental properties

Example:

> Fire + Claw → Fire-enhanced melee attack.

---

## 5. Defense

Represents how the player reacts to incoming damage or hostile effects.

Components can add:

* Armor
* Resistance
* Shields
* Damage reflection
* Thorns
* Elemental retaliation
* Damage reduction

Example:

> Lightning + Defense → attackers receive electrical damage.

Therefore, an elemental component does not automatically mean offensive usage.

---

## 6. Ability

Represents an active or special player ability that is separate from the basic attack.

Examples:

* Dash
* Teleport
* Shockwave
* Healing
* Grapple
* Area attack

Components can:

* Add abilities
* Modify existing abilities
* Change ability behavior
* Reduce cooldowns
* Add elemental effects

Example:

> Fire + Dash → dash leaves a burning trail.

---

## 7. Movement

Represents the player's movement capabilities.

Examples:

* Movement speed
* Jump height
* Dash distance
* Flight
* Wall climbing
* Teleportation

Example:

> Wing component → enhanced aerial movement.

Another component could modify it:

> Fire + Wing → movement leaves a fire trail.

---

## 8. Projectile

Represents projectiles created by the player.

Components can modify:

* Projectile damage
* Element
* Speed
* Range
* Size
* Quantity
* Homing
* Explosion
* Status effects

Example:

> Lightning + Projectile → lightning projectile.

---

## 9. Enemy

Represents an enemy affected directly by a player's effect.

Examples:

* Damage
* Burn
* Poison
* Slow
* Stun
* Knockback
* Marking
* Debuffs

Example:

```text id="p7n1km"
Trigger: On Hit
Target: Enemy

Effect:
  Apply Burn
```

---

## 10. Environment

Represents objects or systems in the game world.

Examples:

* Doors
* Switches
* Platforms
* Water
* Fire
* Electricity
* Destructible objects
* Interactive machinery

This target is particularly important for future systemic gameplay.

Example:

> Fire effect + Environment → burnable objects can catch fire.

This target will be developed further if environmental interactions become a major gameplay feature.

---

# 11. Targets Can Be Chained

An effect does not necessarily stop after affecting its initial target.

Example:

```text id="c4v8n2"
Player Attack
      ↓
Enemy
      ↓
Burn
      ↓
Explosion
      ↓
Nearby Enemies
```

This allows effects to create secondary effects.

However, we should impose safeguards later to prevent infinite effect chains.

---

# 12. Target Selection

A component should specify which targets its effects are compatible with.

Example:

```text id="m7x2pd"
Fire Core

Fire Damage
Allowed Targets:
  Attack
  Ability
  Projectile
  Enemy
  Defense
  Environment
```

This does **not** mean Fire will automatically affect all of them.

It means those targets are valid destinations.

The build and interaction rules determine where the effect actually applies.

---

# 13. Primary vs Secondary Targets

We should distinguish between:

### Primary Target

The thing the component directly modifies.

Example:

> Fire → Attack

### Secondary Target

Something affected as a consequence.

Example:

> Fire Attack → Enemy → Burn

This distinction will become important for the Synergy Engine.

---

# 14. Target Compatibility

Not every effect should be allowed on every target.

For example:

### Movement Speed

Valid:

* Player
* Movement

Invalid:

* Projectile
* Environment

### Fire Damage

Potentially valid:

* Attack
* Ability
* Projectile
* Defense
* Enemy
* Environment

### Armor

Valid:

* Player
* Defense

This compatibility should be defined in the Effect/Target rules rather than manually for every build.

---

# 15. Build Resolution Example

Player equips:

**Claw + Fire Core + Dash**

### Claw

Provides:

```text
Melee Attack
Target: Attack
```

### Fire Core

Provides:

```text
Fire Damage
Compatible Target:
Attack
Ability
Projectile
Defense
Environment
```

### Dash

Provides:

```text
Dash
Target: Movement
```

The system resolves:

```text id="4o9h2s"
Claw
  ↓
Attack
  ↑
Fire
```

and:

```text id="m2z7vk"
Dash
  ↓
Movement
```

The resulting build is:

> **Fire-enhanced melee combat + Dash movement**

---

# 16. Important Design Principle

A component should **not directly say**:

> "I create Fire Claw."

Instead:

```text id="4g8j3s"
Component
   ↓
Effect
   ↓
Compatible Target
   ↓
Generic Resolution
   ↓
Final Gameplay Behavior
```

This is what allows the same Fire component to work with:

* Claw
* Blade
* Projectile
* Shield
* Dash
* Ability

without creating separate Fire versions of every component.

---

# 17. Current Target Model

### Core targets

* Player
* Attack
* Defense
* Ability
* Movement
* Projectile
* Enemy
* Environment

### Key principles

1. Effects are separate from targets.
2. Components provide effects rather than complete builds.
3. Effects can have multiple compatible targets.
4. Targets can contain multiple effects.
5. Effects may create secondary effects.
6. Target compatibility is rule-driven.
7. The system should avoid component-specific combination code.

---

## 18. Open Questions

* Whether a component can have multiple primary targets simultaneously.
* How the system chooses between multiple compatible targets.
* Whether the player explicitly assigns a component to a target.
* Whether the system automatically finds the best target.
* How conflicting target assignments are handled.
* How secondary target chains work.
* Maximum effect-chain depth.
* Whether environment interactions are part of the first version.
