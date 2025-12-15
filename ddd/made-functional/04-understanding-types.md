# 4. Understanding Types

We will represent the domain-driven requirements using an "algebraic type system."

## Types and Functions

A *type* in functional programming is not the same as a *class* in object-oriented programming. A
*type* is just the name given to the set of possible values that can be used as inputs or outputs of
a function.

> [!IMPORTANT]
> "Values" vs "Objects" vs "Variables"
> A value is just a member of a type.
>
> Values are immutable (which is why they are not called "variables") and values do not have any
> behavior attached to them, they are just data.
>
> In contrast, an object is an encapsulation of a data structure *and* its associated behavior (
> methods).
>
> So in the world of functional programming, you should use the term "value" rather than "variable"
> or "object".

## Composition of Types

In functional programming, we use composition to build new functions from smaller functions and new types from smaller types.

New types are built from smaller types in two ways:

- By _AND_ing them together
- By _OR_ing them together

### "AND" Types

For example, we might say that to make a fruit salad you need an apple *and* a banana *and* some cherries.

This kind of type is called a *product type* or a *record*.

```kotlin
data class FruitSalad(
    val apple: AppleVariety,
    val banana: BananaVariety,
    val cherries: CherryVariety,
)
```

### "OR" Types

For example, we might say that for a fruit snack you need an apple *or* a banana *or* some cherries.

A choice type like this is called a *sum type*.

```kotlin
sealed interface FruitSnack
enum class AppleVariety: FruitSnack { GoldenDelicious, GrannySmith, Fuji }
enum class BananaVariety: FruitSnack { Cavendish, GrosMichel, Manzano }
enum class CherryVariety: FruitSnack { Montmorency, Bing }
```

> [!NOTE]
> *Sum types* or *tagged unions*, in F# terminology, are called *discriminated unions*. We will often call them *choice types*, because it best represents their role in domain modeling.

### Simple Types

We often define a choice type with only *one* choice as an easy way to create a "wrapper"--a type that contains a primitive (such as a string or int) as an inner value.

```kotlin
@JvmInline
value class ProductCode(val value: String)
```

### Algebraic Type Systems

An algebraic type system is simply one where every compound type is composed from smaller types by *AND*-ing or *OR*-ing them together.
