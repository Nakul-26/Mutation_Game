# Game #2 — Prototype v0.1 Specification

**Status:** 🔒 Prototype Scope
**Version:** 0.1

---

# 1. Purpose

Prototype v0.1 exists to answer one question:

> **Can our component, effect, target, property, and reaction architecture create genuinely different gameplay from a small number of components?**

This is a **mechanics prototype**, not the first version of the complete game.

---

# 2. Prototype Goal

The prototype must demonstrate:

```text
Component
    ↓
Property / Trait
    ↓
Effect
    ↓
Target
    ↓
Generic Rules
    ↓
Reaction / Synergy
    ↓
Playable Behavior
```

The system must work without creating a manually coded result for every component combination.

---

# 3. Prototype Components

The prototype will contain:

**4 components**

The exact four components will be designed in the next document.

They should intentionally represent different gameplay roles so that the architecture is properly stress-tested.

The four components should collectively test:

* Offensive modification
* Elemental/property effects
* Defensive/adaptation behavior
* Movement or active ability behavior
* Component interactions
* Property reactions

---

# 4. Player

The player starts with:

### Basic Movement

Standard movement appropriate for the eventual 2D/2.5D game.

### Basic Attack

A simple melee attack.

The player has **no components equipped initially**.

Therefore:

```text id="3x7m4q"
Player
├── Basic Movement
└── Basic Melee Attack
```

---

# 5. Component Capacity

For the prototype, the player can equip up to:

**4 active components**

The full game's six-slot system is not required for Prototype v0.1.

The prototype uses a simplified loadout:

```text id="8m2p7x"
[ Component A ]
[ Component B ]
[ Component C ]
[ Component D ]
```

The full:

```text
2 Offensive
2 Adaptation
2 Ability
```

slot structure will be introduced after the core system is validated.

---

# 6. Test Arena

The prototype contains:

**One small test arena.**

The arena should contain enough space to test:

* Melee combat
* Movement
* Defensive effects
* Projectiles/abilities if required
* Enemy interactions
* Component changes

No level progression is required.

---

# 7. Enemy

The prototype should initially use a small number of simple enemies.

At minimum:

### Basic Enemy

Used primarily to test:

* Damage
* Status effects
* Attack interactions
* Reactions
* Build differences

The enemy does not need complex AI initially.

Additional test enemies may be added when a particular mechanic requires them.

---

# 8. Component Acquisition

The prototype should support obtaining components.

Initially, this can be extremely simple.

Example:

```text id="6p4m9x"
Component Pickup
       ↓
Player obtains Component
       ↓
Player equips Component
       ↓
Build recalculates
```

A polished loot system is not required.

Components may be placed directly in the test arena for testing.

---

# 9. Component Management

The prototype must support:

* Equip
* Unequip
* Replace
* Add
* Remove

Every change should trigger Build Resolution.

Example:

```text id="9k4x2m"
A
↓
A + B
↓
A + B + C
↓
Remove B
↓
A + C
↓
Replace C with D
↓
A + D
```

---

# 10. Build Resolution

The prototype must use the planned Build Resolution pipeline:

```text id="7x2m5q"
Active Components
        ↓
Collect Effects
        ↓
Validate
        ↓
Resolve Targets
        ↓
Apply Modifiers
        ↓
Resolve Generic Interactions
        ↓
Resolve Reactions / Synergies
        ↓
Resolve Conflicts
        ↓
Final Build State
```

No component combination should bypass this architecture.

---

# 11. Combination Testing

With four components, every non-empty combination must be tested.

### One component

**4 combinations**

### Two components

**6 combinations**

### Three components

**4 combinations**

### Four components

**1 combination**

### Total

**15 combinations**

All 15 must produce a valid build state.

---

# 12. Order Independence

The prototype must verify that component order does not accidentally change the result.

For example:

```text id="q7m2x9"
A + B + C
```

must produce the same result as:

```text id="5k8p3m"
C + A + B
```

unless order-dependent behavior is deliberately introduced later.

---

# 13. Dynamic Build Testing

The following sequence must work correctly:

```text id="2v7m4x"
Start
 ↓
No Components
 ↓
Add A
 ↓
Add B
 ↓
Add C
 ↓
Remove B
 ↓
Add D
 ↓
Remove A
 ↓
Add B
```

The player's behavior must always reflect the current active components.

---

# 14. Property Testing

The prototype must demonstrate that properties are reusable.

For example, if a component provides:

```text id="6m3x8q"
FIRE
```

the Fire property should be usable by the generic systems regardless of which component provides it.

The architecture must not depend on:

> "Fire only comes from Fire Core."

---

# 15. Target Testing

The prototype should demonstrate that effects can target different gameplay systems.

At minimum, the prototype should test effects affecting:

* Attack
* Defense or player state
* Movement or ability

This verifies that an elemental/property component is not inherently restricted to attack behavior.

---

# 16. Reaction Testing

The prototype should contain only a **small number of reactions**.

The purpose is to verify the reaction architecture rather than create a large reaction catalogue.

A reaction should be triggered by properties/traits/context rather than requiring a hardcoded component pair.

Example concept:

```text id="8r3m6p"
Property A
+
Property B
+
Compatible Context
        ↓
Reaction
        ↓
New Effect
```

The exact reactions will be defined after the four components are selected.

---

# 17. No Combination-Specific Code

The prototype must avoid logic such as:

```text
if A + B
    do X

if A + C
    do Y

if A + B + C
    do Z
```

The components should communicate through:

* Properties
* Traits
* Effects
* Targets
* Generic Rules
* Reactions

---

# 18. Prototype UI

The UI should be minimal.

Required functionality:

* View available components
* Equip components
* Remove components
* See currently equipped components
* Reset build

Optional debugging information may show:

* Active properties
* Active effects
* Active traits
* Triggered reactions
* Final resolved build

The debug UI is highly encouraged during development.

---

# 19. Debugging Requirements

The prototype should make it easy to answer:

> **"Why did this effect happen?"**

For example:

```text id="6n2p7v"
Attack
├── Physical Damage
│   └── Source: Basic Attack
│
├── Fire Damage
│   └── Source: Component A
│
└── Overload
    └── Source: Reaction #03
```

This will be extremely useful when the system becomes more complex.

---

# 20. Performance

Build resolution should happen primarily when the loadout changes.

It should not rebuild the entire player's build every frame.

```text id="3x8m5q"
Loadout Change
      ↓
Resolve Build
      ↓
Cache Result
      ↓
Gameplay Uses Cached Build
```

Individual effects may execute during gameplay based on their triggers.

---

# 21. What Is NOT Included

Prototype v0.1 does **not** include:

* Final story
* Final art
* Final UI
* Multiple levels
* World map
* Bosses
* Large enemy roster
* Full inventory system
* Full loot system
* Permanent progression
* Complete roguelite structure
* Monetization
* Ads
* Online functionality
* Final audio
* Final animations
* Final balancing

These are intentionally postponed.

---

# 22. Success Criteria

Prototype v0.1 is considered successful only if:

### System

* Components can be added and removed.
* Build resolution works reliably.
* Effects attach to valid targets.
* Properties and traits work independently of component identity.
* Reactions work through the designed system.

### Gameplay

* One-component builds are playable.
* Two-component builds meaningfully differ.
* Three-component builds create further variation.
* Four-component builds feel like a distinct build.

### Architecture

* No combination-specific explosion of code.
* Adding a new component does not require rewriting the core engine.
* Component order does not accidentally affect results.
* Removing a component correctly removes its derived effects.
* The system is understandable and debuggable.

### Most Important

> **The prototype must actually be fun enough to justify continuing development.**

Technical elegance alone is not sufficient.

---

# 23. Development Rule

We will **not proceed to a fifth component** simply because the first four technically work.

The four components must first be:

1. Implemented.
2. Tested individually.
3. Tested in all 15 combinations.
4. Playtested.
5. Balanced enough to identify meaningful differences.
6. Evaluated for architectural problems.
7. Evaluated for fun.

Only after that will the next component be designed.

---

# 24. Prototype Completion Gate

```text id="7m4x9q"
4 Components
      ↓
15 Combination Tests
      ↓
Dynamic Loadout Tests
      ↓
Reaction Tests
      ↓
Gameplay Playtesting
      ↓
Architecture Review
      ↓
PASS?
 ┌────┴────┐
YES        NO
 ↓          ↓
Component  Fix / Redesign
#5         ↓
          Retest
```

---

# 25. Core Principle

> **Prototype the system, not the content.**

The purpose of Prototype v0.1 is to prove that a small number of components can create a scalable and enjoyable build system.

If the foundation works with four components, we can confidently expand it.

If it does not work, we fix the foundation before adding content.
