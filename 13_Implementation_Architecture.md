# Game #2 — Implementation Architecture

**Status:** 🟡 Working Design
**Version:** 0.1

---

# 1. Purpose

This document translates the Component, Effect, Target, Property, Rule, Reaction, and Build systems into an implementation architecture.

The architecture must prioritize:

* Simplicity
* Scalability
* Debuggability
* Low coupling
* Data-driven configuration
* Solo-developer maintainability

---

# 2. Core Architecture

The prototype should be structured around:

```text id="n4v7k2"
Component Data
      ↓
Effect Data
      ↓
Build Resolver
      ↓
Rule / Reaction Engine
      ↓
Build State
      ↓
Gameplay Systems
```

Components should primarily contain **data**.

Systems should contain **behavior**.

---

# 3. Component Data

A component should be represented as data.

Conceptually:

```text id="f7m2q8"
ComponentData
├── ID
├── Name
├── Category
├── Roles
├── Properties
├── Traits
├── Effects
├── Modifiers
├── Unique?
└── Upgrade Data
```

The component should not contain large amounts of custom gameplay code.

---

# 4. Effect Data

Effects should also be data-driven.

Conceptually:

```text id="k3x8m1"
EffectData
├── Type
├── Target
├── Value
├── Duration
├── Trigger
├── Conditions
├── Properties
├── Traits
└── Stacking Rule
```

The actual systems interpret this data.

---

# 5. Properties

Properties should preferably be represented using a reusable identifier system.

Conceptually:

```text id="p8m2v6"
Property:
    FIRE
    LIGHTNING
    POISON
    HEAT
    ELECTRIC
    ...
```

The system should not require a separate class for every property.

Properties should primarily be **data identifiers/tags**.

---

# 6. Traits

Traits work similarly.

Examples:

```text id="x4k7m2"
BURNING
SPREADING
CONDUCTIVE
EXPLOSIVE
REGENERATIVE
...
```

Traits describe behavioral characteristics.

The exact implementation may eventually merge Properties and Traits if maintaining two systems proves unnecessary.

---

# 7. Runtime Component Instance

There should be a distinction between:

### Component Definition

Static information describing the component.

```text id="4m8q2x"
Fire Core
Properties:
  FIRE
  HEAT
```

### Component Instance

The actual copy currently owned/equipped by the player.

```text id="6x2p9k"
Fire Core Instance
Level: 2
Upgrade State: ...
Source: Current Run
```

This allows multiple copies and progression without modifying the base definition.

---

# 8. Build State

The Build State represents the **resolved result** of the currently equipped components.

Conceptually:

```text id="7m4x8q"
BuildState
├── Active Components
├── Active Properties
├── Active Traits
├── Active Effects
├── Active Modifiers
├── Active Reactions
└── Final Gameplay Configuration
```

BuildState is derived from the equipped components.

It should not be the primary source of truth.

---

# 9. Build Resolver

The Build Resolver converts:

> Equipped Components → BuildState

Conceptually:

```text id="2q7m4x"
BuildResolver
├── Collect Components
├── Collect Properties
├── Collect Traits
├── Collect Effects
├── Resolve Targets
├── Apply Modifiers
├── Run Rules
├── Run Reactions
├── Resolve Conflicts
└── Produce BuildState
```

This is the central system for build calculation.

---

# 10. Rule Engine

The Rule Engine evaluates generic gameplay rules.

It should receive:

* Components
* Properties
* Traits
* Effects
* Targets
* Build State

and determine which rules apply.

Rules should preferably be data-driven.

---

# 11. Reaction Engine

The Reaction Engine handles property/trait interactions.

Example:

```text id="8p3m7q"
HEAT
+
TOXIC
+
SAME TARGET
↓
Reaction
↓
New Effect
```

The Reaction Engine should generate effects rather than directly modifying game objects.

---

# 12. Gameplay Systems

Gameplay systems consume the final BuildState.

Examples:

### Combat System

Uses:

```text id="q2m7x4"
Attack Effects
```

### Movement System

Uses:

```text id="7x3m8p"
Movement Effects
```

### Defense System

Uses:

```text id="5m9q2k"
Defense Effects
```

### Status System

Uses:

```text id="4x8m1q"
Status Effects
```

The gameplay systems should not need to understand where the effect came from.

---

# 13. Event System

Because effects can trigger on events, the prototype should have a basic event mechanism.

Possible events:

```text id="8m3q7x"
OnAttack
OnHit
OnTakeDamage
OnKill
OnDash
OnAbilityUse
OnDeath
```

Example:

```text id="3x7m2q"
OnHit Event
    ↓
Find active effects listening for OnHit
    ↓
Evaluate conditions
    ↓
Apply effects
```

This prevents individual components from having to directly listen to every gameplay action.

---

# 14. Effect Execution

There should be a distinction between:

### Effect Definition

What an effect means.

### Effect Instance

A specific occurrence during gameplay.

Example:

```text id="7q4m8x"
Effect Definition:
Burn

Instance:
Burn applied to Enemy #12
Duration: 4 seconds
Remaining: 2.7 seconds
Source: Player Attack
```

This distinction becomes important for status effects and temporary effects.

---

# 15. Data vs Runtime

The architecture should generally follow:

```text id="3m8q5x"
STATIC DATA
Component Definitions
Effect Definitions
Rule Definitions
Reaction Definitions
        ↓
RUNTIME
Component Instances
Build State
Effect Instances
Events
        ↓
GAMEPLAY
```

Static definitions should not be modified during normal gameplay.

---

# 16. Build Recalculation

The Build Resolver should run when something affecting the loadout changes.

Examples:

* Component equipped
* Component removed
* Component replaced
* Component upgraded
* Component disabled

Process:

```text id="9x4m7q"
Loadout Changed
      ↓
Invalidate BuildState
      ↓
Resolve Build
      ↓
Cache New BuildState
```

Gameplay then uses the cached result.

---

# 17. Avoid Direct Component-to-System Coupling

Avoid:

```text id="5q8m2x"
FireComponent
    ↓
CombatSystem
```

Prefer:

```text id="7m3x9q"
FireComponent
    ↓
Fire Effect
    ↓
BuildState
    ↓
CombatSystem
```

This allows Fire to later affect:

* Combat
* Defense
* Movement
* Environment

without modifying the Fire component itself.

---

# 18. Avoid Combination-Specific Classes

Do not create classes such as:

```text id="2x7m4q"
FireClaw
FireLightningClaw
PoisonFireClaw
LightningWing
...
```

These would recreate the combination explosion we are trying to avoid.

The system should construct behavior dynamically from data.

---

# 19. Debug Layer

The prototype should have a developer-only debug interface.

It should show:

```text id="7m2x5q"
EQUIPPED
Claw
Fire
Lightning
Wing

PROPERTIES
MELEE
FIRE
HEAT
LIGHTNING
ELECTRIC
AERIAL

EFFECTS
Physical Damage
Fire Damage
Burn
Lightning Damage
Shock
Air Movement

REACTIONS
Overload
```

Ideally, selecting an effect should show its source and reason.

Example:

```text id="4q8m2x"
Fire Damage

Source:
Fire Core

Target:
Attack

Reason:
Generic Elemental Attack Rule
```

This will dramatically simplify debugging.

---

# 20. Error Handling

The system should fail safely.

If a component contains an invalid effect:

```text id="6m3x9q"
Invalid Effect
```

the build resolver should not crash the game.

Instead:

* Log the problem.
* Ignore the invalid effect.
* Continue resolving the remaining build.

Development builds should expose detailed error information.

---

# 21. Testing Architecture

Core systems should be testable independently.

At minimum:

### Component Tests

Verify component data.

### Effect Tests

Verify effect behavior.

### Rule Tests

Verify generic rules.

### Reaction Tests

Verify reactions.

### Build Tests

Verify complete build resolution.

Example:

```text id="3q7m5x"
Input:
Claw + Fire

Expected:
Melee Attack + Fire
```

Another:

```text id="8x2m4q"
Input:
Fire + Lightning

Expected:
Both properties present
+
Expected reaction
```

---

# 22. Prototype Implementation Principle

Do not over-engineer the first version.

We should build only what Prototype v0.1 requires.

For example, we do not need:

* Multiplayer architecture
* Mod support
* Complex save systems
* Procedural content framework
* Large database architecture
* Advanced networking
* Plugin systems

The architecture should be **extensible but simple**.

---

# 23. Recommended High-Level Structure

Conceptually:

```text id="7m4x2q"
GAME
│
├── Data
│   ├── Components
│   ├── Effects
│   ├── Rules
│   └── Reactions
│
├── Core
│   ├── Build Resolver
│   ├── Rule Engine
│   ├── Reaction Engine
│   └── Event System
│
├── Gameplay
│   ├── Combat
│   ├── Movement
│   ├── Defense
│   └── Status
│
└── Debug
    ├── Build Inspector
    ├── Effect Inspector
    └── Event Log
```

The exact folder/class structure will depend on the chosen game engine.

---

# 24. Engine Choice

The implementation engine has **not yet been finalized**.

The architecture is intentionally engine-agnostic.

The eventual choice should prioritize:

* Strong Android support
* Good 2D support
* Suitable 2.5D capabilities
* Fast solo development
* Easy iteration
* Good tooling
* Comfortable programming workflow

The engine decision should be made before implementation begins.

---

# 25. Core Architecture Principle

> **Data defines what exists. Systems define what happens.**

Components, effects, properties, traits, rules, and reactions should primarily describe gameplay data.

Build, combat, movement, defense, event, and reaction systems should interpret that data.

---

# 26. Final Prototype Flow

```text id="8q4m7x"
COMPONENT DEFINITIONS
        ↓
PLAYER LOADOUT
        ↓
BUILD RESOLVER
        ↓
PROPERTIES / TRAITS
        ↓
EFFECTS
        ↓
TARGET RESOLUTION
        ↓
RULE ENGINE
        ↓
REACTION ENGINE
        ↓
BUILD STATE
        ↓
GAMEPLAY SYSTEMS
        ↓
EVENTS
        ↓
EFFECT INSTANCES
        ↓
GAMEPLAY
```

---

## 27. Open Questions

* Final game engine
* Exact programming language
* Exact data serialization format
* Exact class/data structures
* Event architecture
* Rule evaluation implementation
* Reaction evaluation implementation
* BuildState representation
* Debug UI implementation
* Save/load architecture
* Performance profiling strategy
