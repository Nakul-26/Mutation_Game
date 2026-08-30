# Game #2 — Property and Trait System

**Status:** 🟡 Working Design
**Version:** 0.1

---

## 1. Purpose

The Property and Trait System describes the **intrinsic characteristics** of components and effects.

The goal is to avoid making every combination a manually programmed interaction.

Instead of thinking:

> Fire + Poison = Toxic Flame

we want the system to understand:

> Fire has certain properties. Poison has certain properties. Those properties can interact through general rules.

---

# 2. Property vs Trait

For the initial design, we will use the following distinction:

### Property

A characteristic that describes what something **is or contains**.

Examples:

* FIRE
* POISON
* LIGHTNING
* HEAT
* TOXIC
* ELECTRIC
* BIOLOGICAL
* MECHANICAL
* ELEMENTAL

### Trait

A characteristic that describes **how something behaves**.

Examples:

* BURNING
* SPREADING
* CONDUCTIVE
* EXPLOSIVE
* REGENERATIVE
* CORROSIVE
* HOMING
* PIERCING

Properties identify characteristics.

Traits describe behavior.

The distinction is conceptual and may be simplified during implementation if maintaining two separate systems provides no practical benefit.

---

# 3. Components Provide Properties and Traits

A component can provide multiple properties and traits.

Example:

```text id="4q7m2x"
Fire Core

Properties:
  FIRE
  ELEMENTAL
  HEAT

Traits:
  BURNING
  SPREADING
```

Another:

```text id="8m3v6k"
Poison Gland

Properties:
  POISON
  BIOLOGICAL
  TOXIC

Traits:
  DAMAGE_OVER_TIME
  SPREADING
  CORROSIVE
```

The components themselves do not need to know what happens when they are combined.

---

# 4. Effects Can Also Have Properties and Traits

Effects inherit or generate relevant properties.

Example:

```text id="2n7x4p"
Fire Damage

Properties:
  FIRE
  ELEMENTAL

Traits:
  DAMAGE
  BURNING
```

This allows the Rule and Reaction systems to reason about effects rather than only components.

---

# 5. Properties Should Be Reusable

A property should not belong exclusively to one component.

For example:

**FIRE** could come from:

* Fire Core
* Magical artifact
* Fire creature body part
* Experimental weapon
* Future component

All of them can participate in the same rules because they share the property.

---

# 6. Traits Should Describe Behavior

Traits are intended to represent reusable gameplay behavior.

Examples:

### BURNING

Can cause:

* Damage over time
* Ignition
* Fire spread

### CONDUCTIVE

Can:

* Carry Lightning
* Increase electrical chain behavior
* Transfer electrical effects

### SPREADING

Can:

* Transfer an effect from one target to another

### EXPLOSIVE

Can:

* Create area damage
* Trigger on specific conditions

These behaviors should be implemented by generic systems wherever possible.

---

# 7. Property Composition

Multiple properties can coexist.

Example:

```text id="v7x2qa"
Fire Core

Properties:
  FIRE
  ELEMENTAL
  HEAT

Traits:
  BURNING
  SPREADING
```

The system does not need to create a new object called:

> "Fire Component Type 7."

It simply works with the collection of properties and traits.

---

# 8. Example: Fire + Poison

Suppose:

```text id="g5n8kc"
Fire:
  FIRE
  HEAT
  BURNING
  SPREADING

Poison:
  POISON
  TOXIC
  CORROSIVE
  SPREADING
```

The system sees:

```text
FIRE
HEAT
BURNING
POISON
TOXIC
CORROSIVE
SPREADING
```

A reaction rule may detect:

```text
HEAT + TOXIC
```

and produce:

> Toxic Combustion

The important point is that the reaction does not necessarily need to be defined as:

> FIRE + POISON

It can be based on the underlying properties.

---

# 9. Example: Fire + Lightning

Fire:

```text
HEAT
BURNING
```

Lightning:

```text
ELECTRIC
CONDUCTIVE
```

A reaction rule could be based on:

```text
ELECTRIC + HEAT
```

rather than specifically:

```text
FIRE + LIGHTNING
```

This allows other future components containing:

> HEAT

or:

> ELECTRIC

to potentially participate automatically.

---

# 10. Properties Can Exist on Different Targets

Properties are not inherently restricted to attacks.

For example:

```text id="2x6n7v"
FIRE
Target: ATTACK
```

produces an offensive fire effect.

But:

```text id="6q4m1p"
FIRE
Target: DEFENSE
```

could produce a defensive fire reaction.

Likewise:

```text id="9x3v5c"
ELECTRIC
Target: MOVEMENT
```

could create an electrical movement effect.

The Target Model determines where the property/effect can be applied.

---

# 11. Property Strength

Properties may optionally have a magnitude.

Example:

```text id="5c7m1x"
FIRE
Strength: 20
```

Another source:

```text id="8v2q6n"
FIRE
Strength: 50
```

The system can therefore distinguish between:

> Weak Fire

and:

> Strong Fire

without creating separate property types.

The exact numerical model will be determined later.

---

# 12. Property Sources

The same property can originate from different sources.

Example:

```text id="0m6k8p"
FIRE
├── Fire Core
├── Fire Weapon
├── Fire Creature Organ
└── Magical Artifact
```

The Reaction System should generally care about:

> **What properties are currently present?**

rather than:

> **Which exact component supplied them?**

This is important for scalability.

---

# 13. Trait Accumulation

Multiple components can contribute the same trait.

Example:

```text id="5y9r2k"
Component A
→ SPREADING

Component B
→ SPREADING
```

The system can determine whether this results in:

* Increased spread range
* Increased spread chance
* Increased number of targets
* No additional benefit
* A threshold-based behavior

The exact stacking behavior belongs to the Effect/Modifier system.

---

# 14. Thresholds

Properties and traits can potentially activate behaviors when they reach a threshold.

Example:

```text id="1r7x3m"
FIRE Strength >= 100
```

could activate:

> Intense Burning

Another:

```text id="4p8v2q"
ELECTRIC Strength >= 100
```

could activate:

> Chain Lightning

This allows progression without requiring entirely new components.

---

# 15. Context Matters

A property does not necessarily produce the same result everywhere.

For example:

```text id="8q3k6v"
FIRE + ATTACK
→ Burning Attack
```

while:

```text
FIRE + ENVIRONMENT
→ Ignite Object
```

and:

```text
FIRE + DEFENSE
→ Burning Retaliation
```

The property system therefore works together with the Target Model.

---

# 16. Property Interaction Philosophy

We should prioritize interactions based on **shared characteristics**.

Prefer:

```text
HEAT + TOXIC
```

over:

```text
FIRE + POISON
```

when the broader rule makes sense.

Prefer:

```text
ELECTRIC + CONDUCTIVE
```

over:

```text
LIGHTNING + WATER
```

when possible.

Specific component-to-component interactions should be used only when necessary.

---

# 17. Not Every Property Needs a Reaction

Properties can simply coexist.

Example:

```text
FIRE
+
SHADOW
```

may simply result in:

```text
Fire Damage
+
Shadow Damage
```

unless we deliberately create a meaningful interaction.

This prevents the system from generating unnecessary complexity.

---

# 18. Property and Trait Discovery

Properties and traits may eventually become part of the player's experimentation experience.

The player might discover:

> "This material is conductive."

or:

> "This organism spreads poison."

This can make experimentation itself part of gameplay.

Whether properties are explicitly shown to the player or partially hidden will be decided later.

---

# 19. Scalability Goal

The desired relationship is:

```text id="9h5x2m"
Many Components
       ↓
Small Set of Reusable Properties
       ↓
Reusable Traits
       ↓
Generic Interaction Rules
       ↓
Emergent Gameplay
```

Adding a new component should primarily mean assigning it appropriate properties, traits, and effects.

The existing interaction system should handle many of its behaviors automatically.

---

# 20. Important Constraint

We should avoid creating an enormous number of properties.

If every component introduces several unique properties, the system will simply move the complexity from:

> Components

to:

> Properties.

The initial property vocabulary should therefore remain **small, reusable, and meaningful**.

---

# 21. Initial Conceptual Property Groups

The exact vocabulary is not finalized, but possible groups include:

### Elements

* Fire
* Lightning
* Poison
* Shadow
* Ice
* Future elements

### Physical

* Melee
* Projectile
* Impact
* Sharp
* Heavy

### Biological

* Organic
* Regenerative
* Toxic
* Adaptive

### Mechanical

* Energy
* Conductive
* Magnetic
* Mechanical

### Behavioral

* Burning
* Spreading
* Explosive
* Piercing
* Homing
* Chaining

These are starting candidates, not final definitions.

---

# 22. Core Design Principle

The Property and Trait System follows:

> **Describe what something is and how it behaves; let generic systems determine what happens when those characteristics meet.**

This is the primary mechanism we will use to reduce manually authored combination logic.

---

# 23. Current Architecture

```text id="c6m4z8"
COMPONENT
    ↓
PROPERTIES + TRAITS
    ↓
EFFECTS
    ↓
TARGETS
    ↓
GENERIC RULES
    ↓
REACTIONS
    ↓
OPTIONAL SPECIAL SYNERGIES
    ↓
FINAL BUILD
```

---

# 24. Open Questions

* Whether Properties and Traits should remain separate systems.
* Exact property vocabulary.
* Exact trait vocabulary.
* Whether properties have strength/magnitude.
* How traits stack.
* How property thresholds work.
* How reactions are generated.
* How many reactions should be explicitly authored.
* How environmental properties participate.
* Whether the player can inspect properties.
* Whether properties can mutate during a run.
