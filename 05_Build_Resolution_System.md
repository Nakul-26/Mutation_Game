# Game #2 — Build Resolution System

**Status:** 🟡 Working Design
**Version:** 0.1

---

## 1. Purpose

The Build Resolution System converts the player's equipped components into the player's final gameplay behavior.

It answers:

> **"Given these components, what can the player actually do?"**

The system must work automatically and must not require a manually designed result for every possible combination.

---

# 2. Core Pipeline

Whenever the player's active components change:

```text
Equipped Components
        ↓
Collect Effects
        ↓
Validate Effects
        ↓
Resolve Targets
        ↓
Apply Modifiers
        ↓
Resolve Interactions
        ↓
Resolve Synergies
        ↓
Resolve Conflicts
        ↓
Generate Final Build
```

The result is the player's **Active Build State**.

---

# 3. Step 1 — Collect Effects

Each equipped component provides one or more effects.

Example:

```text id="1t6k2v"
Claw
→ Melee Attack

Fire Core
→ Fire Damage

Dash
→ Dash
```

The system gathers all of these effects.

At this stage, they are simply a collection of available effects.

---

# 4. Step 2 — Validate Effects

The system checks whether each effect is valid.

Examples:

* Is the effect enabled?
* Is the component currently equipped?
* Is the effect compatible with the player's current state?
* Is the target valid?
* Are required conditions satisfied?

Invalid effects are removed before further processing.

---

# 5. Step 3 — Resolve Targets

The system determines where each effect should apply.

Example:

```text id="5g9xq4"
Claw
→ Melee Attack

Fire
→ Fire Damage
→ compatible with Attack

Dash
→ Movement
```

The system therefore creates:

```text id="m1k8yc"
Attack:
  Melee
  Fire Damage

Movement:
  Dash
```

---

# 6. Step 4 — Apply Modifiers

Effects that modify other effects are applied.

Example:

```text id="r4k3q1"
Fire Core
→ Fire Damage

Experimental Module
→ +25% Elemental Damage
```

The system resolves:

```text id="v8j2s0"
Fire Damage
→ +25%
```

The final value is modified without creating a new component.

---

# 7. Step 5 — Resolve Interactions

The system checks whether effects interact through **generic rules**.

Example:

```text id="q6t9xm"
Attack
+ Fire
```

Generic rule:

> An elemental effect attached to a compatible attack modifies that attack's damage.

Result:

> Fire-enhanced attack.

Another:

```text id="k4m7za"
Defense
+ Lightning
```

Generic rule:

> Lightning attached to Defense triggers an electrical retaliation effect.

Result:

> Electrical defense.

The system is applying rules rather than looking up predefined builds.

---

# 8. Step 6 — Resolve Synergies

After generic interactions are resolved, the system checks for special synergies.

Example:

```text id="p3d8rw"
Fire
+
Lightning
```

Synergy rule:

> Fire + Lightning → Overload

The resulting build receives:

```text id="x7m4qa"
Overload Effect
```

Important:

The synergy is a **rule**, not a new component.

---

# 9. Step 7 — Resolve Conflicts

Some components may produce incompatible effects.

Examples:

* Fire vs Ice
* Flight vs Ground Lock
* Two mutually exclusive weapons
* Conflicting movement modes

The system needs deterministic conflict rules.

Possible resolution strategies:

### Priority

One effect has higher priority.

### Replacement

The newer/stronger effect replaces the previous one.

### Coexistence

Both effects remain active.

### Incompatibility

The player cannot equip both simultaneously.

The exact conflict rules will be defined separately as the system develops.

---

# 10. Step 8 — Generate Final Build

After all effects, modifiers, interactions, synergies, and conflicts are resolved, the system produces the player's final active state.

Example:

```text id="w6n2pc"
BUILD

Attack
├── Melee
├── Physical Damage
├── Fire Damage
├── Lightning Damage
└── Overload

Defense
└── Basic Defense

Movement
└── Dash
```

The gameplay systems consume this final state.

---

# 11. Build State vs Components

The player's **components** and **build state** are different things.

### Components

What the player owns/equips.

Example:

```text
Claw
Fire Core
Lightning Core
Dash
```

### Build State

What those components produce after resolution.

Example:

```text
Fire + Lightning Melee
+
Overload
+
Dash
```

The Build State should be treated as a **derived result**.

It should not be manually stored as the source of truth.

---

# 12. Dynamic Recalculation

The Build Resolution System runs whenever the active component set changes.

Example:

```text id="q1c8vb"
Claw
↓
Claw + Fire
↓
Claw + Fire + Lightning
↓
Remove Fire
↓
Claw + Lightning
↓
Add Dash
↓
Claw + Lightning + Dash
```

Each change causes the system to recalculate the active build.

This ensures the player's abilities always reflect the currently equipped components.

---

# 13. Order Independence

Unless explicitly required by a future mechanic:

```text
Fire + Claw
```

must produce the same result as:

```text
Claw + Fire
```

Likewise:

```text
Fire + Lightning + Claw
```

must produce the same result regardless of the order in which the player equipped them.

This prevents accidental order-dependent behavior.

---

# 14. No Combination Database

The system must **not** depend on a database containing every possible build.

Avoid:

```text id="8c0s4f"
Claw + Fire = Fire Claw
Claw + Lightning = Lightning Claw
Claw + Fire + Lightning = Electro-Flame Claw
...
```

Instead:

```text id="8x2n5m"
Components
    ↓
Effects
    ↓
Targets
    ↓
Generic Rules
    ↓
Synergies
    ↓
Final Build
```

This is essential for scalability.

---

# 15. Example: Four Components

Suppose the player equips:

```text
Claw
Fire
Lightning
Dash
```

### Component effects

```text id="4m1z8p"
Claw
→ Melee Attack

Fire
→ Fire Damage

Lightning
→ Lightning Damage

Dash
→ Dash
```

### Target resolution

```text id="1b7k6c"
Melee Attack
↑
Fire
↑
Lightning

Movement
↑
Dash
```

### Synergy resolution

```text id="m9q2ws"
Fire + Lightning
→ Overload
```

### Final build

```text id="f5r7x3"
Melee Attack
├── Physical Damage
├── Fire Damage
├── Lightning Damage
└── Overload

Movement
└── Dash
```

No special "four-component build" was designed.

The system constructed it.

---

# 16. One Important Constraint

The system should avoid uncontrolled combinations.

For example, if:

```text
Fire → creates Burn
Burn → creates Explosion
Explosion → creates Fire
Fire → creates Burn
```

we could accidentally create an infinite loop.

Therefore, the final implementation must eventually have:

* Effect-chain limits
* Recursion protection
* Trigger limits
* Event processing rules

These will be designed later.

---

# 17. Performance Requirement

Build resolution should normally happen **when the build changes**, not every frame.

For example:

```text
Component equipped
        ↓
Resolve Build
        ↓
Cache Active Build State
        ↓
Gameplay uses cached state
```

We should avoid repeatedly rebuilding the player's entire build during every frame of gameplay.

Individual effects can still execute during gameplay when their triggers occur.

---

# 18. Design Principle

The Build Resolution System follows this principle:

> **The player chooses components. The game determines the resulting build through rules.**

The player should feel like they are **constructing a build**, while the developer only needs to maintain a relatively small set of reusable systems and rules.

---

## 19. Current Architecture

```text
COMPONENT
    ↓
EFFECT
    ↓
TARGET
    ↓
MODIFIER
    ↓
GENERIC INTERACTION
    ↓
SYNERGY
    ↓
CONFLICT RESOLUTION
    ↓
FINAL BUILD STATE
    ↓
GAMEPLAY SYSTEMS
```

---

## 20. Open Questions

* Exact synergy rule structure
* How generic interaction rules are represented
* How priorities are represented
* Exact conflict resolution
* How effect chains are processed
* Whether effects can dynamically create new effects
* Build-state data structure
* Runtime implementation details
* Save/load representation
