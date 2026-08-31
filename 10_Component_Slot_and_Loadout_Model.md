# Game #2 — Component Slot and Loadout Model

**Status:** 🟡 Working Design
**Version:** 0.1

---

## 1. Purpose

The Slot and Loadout Model defines:

* How many components the player can actively use.
* Where components are equipped.
* How components interact with different gameplay targets.
* How the player changes their active build.

The system must provide meaningful choices without making the loadout unnecessarily complicated.

---

# 2. Active Component Capacity

The initial design uses:

**6 active component slots**

These are divided into:

```text id="7c2m8x"
OFFENSIVE
[ 1 ] [ 2 ]

ADAPTATION
[ 1 ] [ 2 ]

ABILITY
[ 1 ] [ 2 ]
```

The player can have many components available but only a limited number active at one time.

---

# 3. Important Principle: Slot Type Is Not the Same as Effect Target

A slot determines **what kind of component role the player is choosing**.

A Target determines **what an effect modifies**.

These are separate concepts.

For example:

```text id="6x9k3p"
Fire Component
    ↓
Equipped in Offensive Slot
    ↓
Provides Fire Effect
    ↓
Target: Attack
```

But another Fire component or configuration could potentially produce:

```text id="5m2v8q"
Fire Component
    ↓
Equipped in Adaptation Slot
    ↓
Provides Fire Effect
    ↓
Target: Defense
```

This separation prevents the slot system from becoming tightly coupled to the Effect System.

---

# 4. Component Role

Each component should declare one or more **roles** it supports.

Possible roles:

* Offensive
* Adaptation
* Ability

Example:

```text id="8f4q1m"
Claw

Roles:
  Offensive
```

Another:

```text id="2p7x5k"
Wing

Roles:
  Adaptation
  Ability
```

Another:

```text id="6z3m8c"
Experimental Module

Roles:
  Offensive
  Adaptation
  Ability
```

This allows components to be flexible without requiring every component to work everywhere.

---

# 5. Component Can Support Multiple Roles

A component may be valid for multiple slot categories.

For example:

> Lightning Core

could potentially support:

```text id="2s8m5q"
Offensive
Adaptation
Ability
```

The player's slot choice can influence how the component is interpreted.

For example:

### Offensive

Lightning modifies attacks.

### Adaptation

Lightning provides electrical defense.

### Ability

Lightning becomes an active electrical ability.

The exact behavior should come from the component's effects and target rules.

---

# 6. Slot Placement as a Player Decision

The player should have some control over how a flexible component is used.

Example:

```text id="v7q4m2"
Lightning Core

Placed in:
OFFENSIVE

→ Lightning-enhanced attacks
```

Move it to:

```text id="m9x2c7"
ABILITY

→ Lightning ability
```

This gives components additional strategic value without requiring multiple versions of the same component.

---

# 7. Component Assignment

When a component is equipped:

```text id="k4n8p2"
Component
   ↓
Check supported roles
   ↓
Check available slot
   ↓
Equip
   ↓
Build Resolution
```

If the component does not support the selected slot type, it cannot be equipped there.

---

# 8. Duplicate Components

The initial system should allow or disallow duplicates based on component design.

Example:

```text id="4x7m9q"
Fire Core
Fire Core
```

Possible behaviors:

### Allow

Both contribute Fire effects.

### Limited

Only one instance of the component may be active.

### Unique

The component can only appear once in the entire loadout.

This should be configurable per component.

The default should be:

> **Duplicates are allowed unless a component is explicitly marked Unique.**

---

# 9. Empty Slots

The player does not need to fill all six slots.

Valid builds include:

```text id="8q5m1v"
[Claw]
[Empty]
[Empty]
[Empty]
[Empty]
[Empty]
```

and:

```text id="5p7x3m"
[Claw]
[Fire]
[Dash]
[Empty]
[Empty]
[Empty]
```

and eventually:

```text id="2r9k6c"
[Claw]
[Fire]
[Lightning]
[Dash]
[Armor]
[Regeneration]
```

This is important for supporting early-game progression and experimentation.

---

# 10. Build Identity

The loadout itself does not define the final build identity.

Instead:

```text id="3m8x2p"
Loadout
    ↓
Components
    ↓
Effects
    ↓
Targets
    ↓
Rules / Reactions
    ↓
Final Build
```

Therefore:

> Two players with the same six components could potentially produce different results if their slot assignment changes the supported target/role.

---

# 11. Example

Suppose the player has:

```text id="8v4q2m"
Claw
Fire
Lightning
Dash
```

Possible loadout:

```text id="4n7x1c"
Offensive:
  Claw
  Fire

Adaptation:
  Lightning
  Empty

Ability:
  Dash
  Empty
```

The result might be:

```text id="5k2p8m"
Attack:
  Melee
  Fire

Defense:
  Lightning

Movement/Ability:
  Dash
```

If Lightning is moved to Offensive:

```text id="6q9m3v"
Offensive:
  Claw
  Lightning
```

the build could instead become:

```text id="8r4x7p"
Attack:
  Melee
  Lightning
```

This makes loadout placement meaningful.

---

# 12. Loadout Changes

The player should be able to change the active loadout whenever the game rules allow it.

Possible opportunities:

* Safe zones
* Between encounters
* At checkpoints
* At special stations
* During runs, depending on the final game design

The exact restriction is not finalized.

---

# 13. Loadout vs Inventory

These should remain separate.

### Inventory

Everything the player currently owns.

Example:

```text id="8m3q7x"
Fire Core
Lightning Core
Claw
Dash
Armor
Poison Gland
Wing
```

### Loadout

What the player currently has active.

Example:

```text id="2v6k9p"
Claw
Fire
Dash
Armor
```

The inventory can contain many components.

The active loadout remains limited.

---

# 14. Run-Specific Loadout

Because the game uses hybrid progression, components can have two states:

### Permanently Unlocked

The player has discovered the component and can potentially obtain/use it in future runs.

### Currently Available

The component has been obtained during the current run.

This distinction allows permanent progression while keeping each run's build choices meaningful.

---

# 15. Loadout Recalculation

Whenever a component is:

* Added
* Removed
* Replaced
* Moved between slots
* Upgraded

the Build Resolution System recalculates the active build.

```text id="7w2m5q"
Loadout Change
      ↓
Build Resolution
      ↓
Effects Recalculated
      ↓
Targets Recalculated
      ↓
Rules/Reactions Recalculated
      ↓
New Active Build
```

---

# 16. Player-Facing Complexity

Although the underlying system may be complex, the player-facing interface should remain simple.

The player should primarily see:

```text id="3q7m1x"
OFFENSE
[ Claw ] [ Fire ]

ADAPTATION
[ Armor ] [ Empty ]

ABILITY
[ Dash ] [ Empty ]
```

Detailed properties, traits, and reactions can be revealed progressively through the UI.

The player should not need to understand the underlying rule engine.

---

# 17. Initial Prototype Simplification

For the first 3–4 component prototype, we do not need the full six-slot interface.

We can temporarily use:

```text id="7m4x2p"
Active Components:
[ A ] [ B ] [ C ] [ D ]
```

Once the underlying component/effect/reaction system is proven, we introduce the full slot structure.

This avoids building UI before the underlying mechanics are validated.

---

# 18. Design Principle

> **Slots create strategic constraints; components provide capabilities; effects determine behavior; the rules and reaction systems determine interactions.**

These systems should remain separate.

---

# 19. Current Model

```text id="4p8m2x"
INVENTORY
    ↓
AVAILABLE COMPONENTS
    ↓
6 ACTIVE SLOTS
    ├── 2 Offensive
    ├── 2 Adaptation
    └── 2 Ability
    ↓
COMPONENT ROLES
    ↓
EFFECTS + TARGETS
    ↓
BUILD RESOLUTION
    ↓
FINAL BUILD
```

---

## 20. Open Questions

* Whether all six slots are available from the beginning.
* Whether slot count can increase through progression.
* Whether components can be moved between slot types freely.
* Whether slot placement changes component behavior or only determines compatibility.
* Exact duplicate-component rules.
* Exact loadout-changing restrictions during runs.
* Whether components can be upgraded while equipped.
* Whether the player can save multiple loadout presets.
* Final player-facing loadout UI.
