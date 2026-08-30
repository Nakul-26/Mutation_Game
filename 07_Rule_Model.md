# Game #2 — Rule Model

**Status:** 🟡 Working Design
**Version:** 0.1

---

## 1. Purpose

The Rule System defines how the game interprets components, effects, tags, targets, and synergies.

The fundamental goal is:

> **Rules describe relationships between properties, not individual component combinations.**

---

# 2. What Is a Rule?

A Rule is essentially:

```text id="r8j3kp"
IF conditions are satisfied
        ↓
THEN perform an action
```

Example:

```text id="c4y8m2"
IF:
  Effect has FIRE tag
  AND
  Target is ATTACK

THEN:
  Add Fire Damage to Attack
```

The rule does not care which component supplied Fire.

---

# 3. Rule Structure

Every rule conceptually contains:

```text id="7p3x0k"
Rule
├── ID
├── Conditions
├── Actions
├── Priority
├── Scope
├── Tags
└── Constraints
```

---

## 4. Conditions

Conditions determine whether a rule should execute.

Conditions can inspect:

### Components

* Component category
* Component tags
* Number of components
* Specific component property

### Effects

* Effect type
* Effect tags
* Effect target
* Effect values

### Build

* Current active effects
* Current build tags
* Number of matching properties
* Existing synergies

### Game State

Potentially later:

* Player health
* Enemy state
* Environment
* Current run state

---

# 5. Condition Examples

### Tag condition

```text id="a2x9vn"
Has Tag: FIRE
```

### Multiple tags

```text id="8c6w1p"
Has Tags:
  FIRE
  ELEMENTAL
```

### Target condition

```text id="2y7kq4"
Target == ATTACK
```

### Count condition

```text id="4s8z1m"
Number of FIRE effects >= 2
```

### Combined condition

```text id="j9p4xb"
Has FIRE
AND
Has LIGHTNING
AND
Target == ATTACK
```

---

# 6. Actions

Actions determine what happens when the conditions are satisfied.

Possible actions include:

* Add Effect
* Modify Effect
* Remove Effect
* Change Target
* Add Tag
* Remove Tag
* Create Status
* Trigger Event
* Modify Value
* Modify Duration
* Modify Cooldown

---

# 7. Example Generic Rule

### Elemental Attack Rule

```text id="6t5y3n"
IF:
  Effect has ELEMENTAL tag
  AND
  Target == ATTACK

THEN:
  Attach elemental effect to Attack
```

This rule allows:

```text
Fire + Claw
Lightning + Claw
Poison + Claw
Shadow + Claw
```

without separate rules.

---

# 8. Example Synergy Rule

### Fire + Lightning

```text id="3m7q2x"
IF:
  Build has FIRE tag
  AND
  Build has LIGHTNING tag

THEN:
  Add OVERLOAD effect
```

This is a synergy rule.

It operates on **properties**, not component names.

---

# 9. Specificity

Rules can have different levels of specificity.

### Level 1 — Universal

```text id="4p1x7c"
ELEMENTAL + ATTACK
```

Very broad.

### Level 2 — Category

```text id="9q3m6a"
FIRE + MELEE
```

More specific.

### Level 3 — Build state

```text id="6k2r8v"
FIRE + LIGHTNING + MELEE
```

Very specific.

We should prefer the **least specific rule that produces the desired gameplay**.

This keeps the system scalable.

---

# 10. Rule Priority

When multiple rules apply, they need deterministic ordering.

Each rule can have a priority.

Example:

```text id="v2m7nq"
Generic Rules:
Priority 10

Modifier Rules:
Priority 20

Synergy Rules:
Priority 50

Major Synergy Rules:
Priority 100
```

Higher-priority rules execute later.

The exact numerical values are not final.

---

# 11. Rule Scope

A rule should define what it examines.

Possible scopes:

### Component

Looks at individual components.

### Effect

Looks at individual effects.

### Target

Looks at a specific target.

### Build

Looks at the player's complete active build.

### Event

Runs when a gameplay event occurs.

This prevents every rule from having to inspect the entire game state.

---

# 12. Rule Example: Fire + Lightning

The engine could evaluate:

```text id="9h2w5c"
BUILD

Tags:
  MELEE
  FIRE
  LIGHTNING
```

Rule:

```text id="2q8m1v"
IF:
  Build has FIRE
  AND
  Build has LIGHTNING

THEN:
  Add effect OVERLOAD
```

The engine produces:

```text id="0y4v7k"
OVERLOAD
Tags:
  ELEMENTAL
  EXPLOSION
  LIGHTNING
```

That new effect can then be processed by the rest of the system.

---

# 13. Rule Independence

Rules should not depend on specific component IDs unless absolutely necessary.

Avoid:

```text id="9m3k5x"
IF ComponentID == FIRE_CORE
AND ComponentID == LIGHTNING_CORE
```

Prefer:

```text id="4n7q2b"
IF Build contains FIRE
AND Build contains LIGHTNING
```

This allows future components to reuse existing rules.

For example:

> A future weapon that naturally has the FIRE tag can trigger the same Fire + Lightning synergy.

---

# 14. Rule Constraints

Rules may optionally have constraints.

Examples:

```text id="5w8k3n"
Maximum Activations: 1
```

or:

```text id="r7v2m4"
Required Target: Attack
```

or:

```text id="6x1p9q"
Cooldown: 2 seconds
```

This prevents uncontrolled repeated activation.

---

# 15. Rule Output

A rule should preferably produce **Effects**, rather than directly manipulating game objects.

Example:

```text id="3f6q8w"
Rule:
Fire + Lightning

Output:
Overload Effect
```

The normal gameplay systems then process Overload.

This keeps the Rule System separate from the actual gameplay implementation.

---

# 16. Rule Chain

Rules can produce effects that cause other rules to become applicable.

Example:

```text id="7k2p9m"
Fire
+
Lightning
↓
Overload
↓
Explosion
↓
Area Damage
```

However, rule chains must have safeguards.

Potential protections:

* Maximum chain depth
* Prevent identical rule repetition
* Event processing limits
* Per-event activation limits

These will be finalized during implementation.

---

# 17. Rule Categories

We should initially organize rules into:

### Interaction Rules

Determine how effects attach to targets.

### Modification Rules

Change effect properties.

### Synergy Rules

Create additional effects from combinations of properties.

### Conflict Rules

Handle incompatible effects.

### Trigger Rules

Determine when conditional effects activate.

### Conversion Rules

Allow one property/effect to transform into another.

---

# 18. Example: Defensive Lightning

Suppose:

```text
Lightning
Target: Defense
```

Generic rule:

```text id="5d8m1q"
IF:
  Effect has LIGHTNING
  AND
  Target == DEFENSE

THEN:
  Add Lightning Retaliation
```

No special Lightning Armor component is required.

---

# 19. Rule Data vs Code

Rules should ideally be represented as **data/configuration** wherever practical.

The underlying engine is coded once.

The actual rules can then be defined as data.

Conceptually:

```text id="8w3n7p"
Rule:
  Required Tags:
    FIRE
    LIGHTNING

  Required Target:
    ATTACK

  Action:
    Add Effect: OVERLOAD
```

This means adding a new synergy later can often involve creating a new rule rather than modifying core gameplay code.

Some highly complex rules may still require custom code.

---

# 20. Design Principle

The Rule System follows:

> **Code the engine. Configure the rules.**

We want the core engine to remain relatively stable while the game's gameplay rules can grow.

---

# 21. Complete Architecture So Far

```text id="6m2r8x"
COMPONENT
    ↓
EFFECT + TAGS
    ↓
TARGET
    ↓
RULE ENGINE
    ├── Generic Rules
    ├── Modifier Rules
    ├── Synergy Rules
    ├── Conflict Rules
    └── Trigger Rules
    ↓
FINAL EFFECT SET
    ↓
FINAL BUILD STATE
    ↓
GAMEPLAY
```

---

# 22. Open Questions

* Exact condition representation
* Exact action representation
* Rule evaluation order
* Rule priority implementation
* Rule chain limits
* Conflict rules
* Whether rules can modify other rules
* Which rules should be data-driven
* Which rules require custom code
* How rules are debugged and visualized
* How discovered synergies are exposed to the player
