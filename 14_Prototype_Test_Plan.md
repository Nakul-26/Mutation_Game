# Game #2 — Prototype Test Plan

**Status:** 🟡 Prototype Validation
**Version:** 0.1

---

# 1. Purpose

This document defines how Prototype v0.1 will be tested before expanding the component system.

The prototype must validate three things:

1. **Technical correctness**
2. **System scalability**
3. **Gameplay quality**

A technically working system is not enough.

---

# 2. Prototype Components

The initial prototype contains:

```text id="7m4x2q"
A — Claw
B — Fire Core
C — Lightning Core
D — Wing Mutation
```

The prototype supports up to four active components.

---

# 3. Combination Coverage

There are:

**15 non-empty combinations**

to test.

### One Component

```text id="8m2x5q"
A
B
C
D
```

### Two Components

```text id="3q7m1x"
A+B
A+C
A+D
B+C
B+D
C+D
```

### Three Components

```text id="5x9m2q"
A+B+C
A+B+D
A+C+D
B+C+D
```

### Four Components

```text id="6m4q8x"
A+B+C+D
```

Every combination must be tested.

---

# 4. Individual Component Tests

Before testing combinations, each component must work independently.

---

## 4.1 Claw

Test:

* Component can be equipped.
* Basic attack changes correctly.
* Damage is correct.
* Removing Claw restores the original attack.
* No unrelated systems are affected.

Expected:

```text id="7q3m5x"
Claw
↓
Melee Attack
```

---

## 4.2 Fire Core

Test:

* Fire property is registered.
* Fire effect is generated.
* Fire can target the intended system.
* Attack receives Fire correctly.
* Burn works if included in the prototype.
* Removing Fire removes its effects.

Expected:

```text id="4m8x2q"
Fire
↓
Fire Effect
↓
Attack
```

---

## 4.3 Lightning Core

Test:

* Lightning property is registered.
* Lightning effect works.
* Lightning can modify the intended target.
* Shock works if included.
* Removing Lightning removes its effects.

---

## 4.4 Wing Mutation

Test:

* Wing modifies movement.
* Movement returns to normal after removal.
* Wing does not unintentionally modify attacks.
* Movement-related effects remain independent of combat.

---

# 5. Two-Component Tests

Each pair must be tested for:

* Correct effects
* Correct targets
* Correct property accumulation
* Correct interaction
* Correct reaction behavior
* Correct removal behavior

---

# 6. Claw + Fire

Expected concept:

```text id="9x3m7q"
Melee Attack
+
Fire
```

Verify:

* Fire modifies the attack.
* Physical damage remains.
* Fire damage is added.
* Burn behaves correctly.
* Removing Fire restores Claw-only behavior.

---

# 7. Claw + Lightning

Expected:

```text id="6q8m2x"
Melee Attack
+
Lightning
```

Verify:

* Lightning modifies the attack.
* Physical damage remains.
* Lightning damage is added.
* Shock behaves correctly if implemented.

---

# 8. Claw + Wing

This is an important architectural test.

Verify:

```text id="5m7q2x"
Attack
+
Movement
```

The two components should coexist without interfering with each other.

---

# 9. Fire + Lightning

This is the first major reaction test.

Verify:

```text id="3x8m5q"
Fire
+
Lightning
```

The system should:

* Detect both properties.
* Apply their individual effects.
* Determine whether the reaction conditions are satisfied.
* Generate the appropriate reaction if defined.
* Avoid requiring a special `FireLightningComponent`.

---

# 10. Fire + Wing

Verify:

```text id="7q2m4x"
Fire
+
Movement
```

The system should determine whether Fire can meaningfully interact with the movement target.

If no interaction is defined:

> Fire and Wing should simply coexist.

A lack of synergy is a valid result.

---

# 11. Lightning + Wing

Same principle.

The system should not force an interaction merely because two components are equipped.

---

# 12. Three-Component Tests

Three-component builds test whether interactions continue to work as the system grows.

---

## 12.1 Claw + Fire + Lightning

This is the most important initial build.

Expected:

```text id="4m8x7q"
Melee
├── Physical
├── Fire
├── Lightning
└── Reaction if applicable
```

Verify:

* Both elements attach to the attack.
* Both effects remain active.
* Elemental reaction is correctly detected.
* No duplicate or infinite reactions occur.
* Damage calculations remain correct.

---

## 12.2 Claw + Fire + Wing

Verify:

* Attack receives Fire.
* Movement receives Wing.
* The systems remain independent.
* Any valid cross-system reaction works correctly.

---

## 12.3 Claw + Lightning + Wing

Same principle.

---

## 12.4 Fire + Lightning + Wing

Verify:

* Fire works.
* Lightning works.
* Wing works.
* Elemental interaction works where appropriate.
* Movement does not accidentally inherit attack-only effects.

---

# 13. Four-Component Test

Test:

```text id="8x3m7q"
Claw
+
Fire
+
Lightning
+
Wing
```

This is the first complete prototype build.

Verify that:

```text id="m5q2x9"
Attack
├── Physical
├── Fire
├── Lightning
└── Reactions

Movement
└── Wing
```

works simultaneously.

---

# 14. Equip/Remove Tests

The system must correctly handle:

```text id="7m4x2q"
Add A
Add B
Add C
Add D
Remove C
Add C
Remove A
Remove B
Add A
```

After every operation:

> The BuildState must correctly reflect the current loadout.

---

# 15. Replacement Tests

Test:

```text id="2q8m5x"
Claw + Fire
```

Replace Fire:

```text id="4m7x2q"
Claw + Lightning
```

Verify:

* Fire effects disappear.
* Lightning effects appear.
* Old reactions disappear if no longer valid.
* New reactions appear if applicable.

---

# 16. Order Independence Tests

Test:

```text id="5x7m2q"
Claw → Fire → Lightning
```

and:

```text id="8m3q6x"
Lightning → Claw → Fire
```

and:

```text id="1q9m4x"
Fire → Lightning → Claw
```

All should resolve to the same final build.

---

# 17. Property Independence Tests

Create two different components that provide the same property during testing.

Verify that a generic rule responds to the property rather than the component identity.

Example:

```text id="3m8x5q"
Component A
→ FIRE
```

and:

```text id="7q2m9x"
Component B
→ FIRE
```

Both should be recognized as:

```text id="0x4m7q"
FIRE
```

by the interaction system.

---

# 18. Reaction Tests

For every reaction implemented in the prototype, test:

### Activation

Does the reaction occur when requirements are met?

### Non-activation

Does it remain inactive when requirements are not met?

### Removal

Does it disappear when a required property is removed?

### Context

Does it occur only on the intended target/event?

### Repetition

Does it avoid unintended repeated activation?

### Chain Safety

Can it create an infinite reaction chain?

It must not.

---

# 19. Negative Tests

The system must also test things that **should not happen**.

Examples:

```text id="8m5q2x"
Fire
```

should not automatically create:

> Fire + Lightning reaction.

If Lightning isn't present, no reaction should occur.

Similarly:

```text id="4q7m3x"
Fire → Defense
Lightning → Attack
```

should not automatically create an Attack-based Fire/Lightning reaction if the reaction requires both effects on the same target.

---

# 20. Conflict Tests

If conflicts are introduced during the prototype, test:

* Conflicting effects
* Duplicate effects
* Conflicting targets
* Multiple modifiers
* Multiple reactions

The system must resolve them deterministically.

---

# 21. Reset Test

The prototype must provide:

> **Reset Build**

This should:

```text id="7q4m2x"
Remove all components
        ↓
Clear derived effects
        ↓
Clear reactions
        ↓
Restore basic player state
```

Expected result:

> Player returns to the original no-component state.

---

# 22. Save/Reload Test

If save/load is implemented in the prototype:

```text id="4m8q2x"
Save:
Claw + Fire + Wing

Reload

Expected:
Claw + Fire + Wing
```

The BuildState should be regenerated from the saved loadout rather than being treated as the saved source of truth.

---

# 23. Performance Tests

Measure:

* Build resolution time
* Number of effects
* Number of active rules
* Reaction processing time
* Runtime event processing

Build resolution should not occur every frame.

Gameplay should use the resolved/cached BuildState.

---

# 24. Debugging Tests

The debug inspector should answer:

> **Why does the player currently have this behavior?**

For example:

```text id="9m2x5q"
Fire Damage

Source:
Fire Core

Property:
FIRE

Target:
ATTACK

Trigger:
ON_HIT

Applied By:
Generic Elemental Attack Rule
```

For a reaction:

```text id="3x7m8q"
OVERLOAD

Sources:
Fire Core
Lightning Core

Requirements:
FIRE ✓
LIGHTNING ✓
SAME TARGET ✓

Created By:
Reaction #01
```

---

# 25. Gameplay Evaluation

Technical tests are not enough.

Each combination should be evaluated for:

### Feel

Does the build feel different?

### Clarity

Can the player understand what changed?

### Fun

Does the combination make gameplay more interesting?

### Choice

Would a player actually choose one build over another?

### Power

Is one combination obviously superior?

### Discovery

Does finding a new interaction feel rewarding?

---

# 26. Build Diversity Test

After testing all 15 combinations, ask:

> **Do the combinations actually feel different?**

If:

```text id="7m4x2q"
A+B
A+C
A+D
```

all feel almost identical, then the system technically works but the design does not.

We should modify the components/effects before adding more content.

---

# 27. Architecture Stress Test

After implementing all four components, attempt to add a hypothetical:

> **Component E**

without modifying the core:

* Build Resolver
* Rule Engine
* Reaction Engine
* Combat System

Ideally, adding Component E should primarily involve:

```text id="4q8m2x"
Create Component Data
+
Define Effects
+
Define Properties/Traits
```

If significant engine changes are required, the architecture needs review.

---

# 28. Completion Gate

Prototype v0.1 passes only if:

```text id="8m3q7x"
15 Combinations Tested
        ↓
Dynamic Loadout Works
        ↓
Properties Work
        ↓
Effects Work
        ↓
Targets Work
        ↓
Reactions Work
        ↓
No Combination-Specific Architecture
        ↓
Builds Are Meaningfully Different
        ↓
Gameplay Is Fun
        ↓
Architecture Survives Component E
```

Only then do we proceed to additional components.

---

# 29. Final Decision

### PASS

Proceed to:

> **Component #5**

### PARTIAL PASS

Fix the specific system/component causing problems and retest.

### FAIL

Stop adding content.

Reconsider the architecture or component design before continuing.

---

# 30. Core Principle

> **We don't add more content to hide problems in the foundation.**

The first four components must prove that:

> **A small number of well-designed components + a strong systemic architecture can produce meaningful gameplay variety.**
