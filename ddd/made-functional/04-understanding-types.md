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

- By *AND*-ing them together
- By *OR*-ing them together

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
> *Sum types* or *tagged unions*, in F# terminology, are called *discriminated unions*. We will often call them *choice types*, because this term best represents their role in domain modeling.

### Simple Types

We often define a choice type with only *one* choice as an easy way to create a "wrapper"--a type that contains a primitive (such as a string or int) as an inner value.

```kotlin
@JvmInline
value class ProductCode(val value: String)
```

### Algebraic Type Systems

An algebraic type system is simply one where every compound type is composed from smaller types by *AND*-ing or *OR*-ing them together.

## Working with Types

In Kotlin, the way types are defined and the way that they are constructed are very similar.

For example, we can define a record type, like this:

```kotlin
data class Person(val first: String, val last: String)
```

A choice type is defined as follows:

```kotlin
sealed interface OrderQuantity {
    @JvmInline
    value class UnitQuantity(val value: Int): OrderQuantity

    @JvmInline
    value class KilogramQuantity(val value: Double): OrderQuantity
}
```

A choice type is constructed by instantiating any one of the specific subtypes:

```kotlin
val anOrderQtyInUnits = OrderQuantity.UnitQuantity(10)
val anOrderQtyInKg = OrderQuantity.KilogramQuantity(2.5)
```

To deconstruct a choice type, we use pattern matching. In the same way that we constructed the type by choosing one of the subtypes, we consume it by handling each subtype.

In Kotlin, this is done using a `when` expression:

```kotlin
when (orderQuantity) {
    is OrderQuantity.UnitQuantity -> println("${orderQuantity.value} units")
    is OrderQuantity.KilogramQuantity -> println("${orderQuantity.value} kg")
}
```

## Building a Domain Model by Composing Types

A composable type system is a great aid in doing DDD because we can quickly create a complex model simply by mixing types together in different combinations. For example, suppose we want to track payments for an e-commerce site.

We start with some wrappers for primitive types:

```kotlin
@JvmInline
value class CheckNumber(val value: Int)

@JvmInline
value class CardNumber(val value: String)
```

Next, we build up low-level types. A `CardType` is an OR type, while `CreditCardInfo` is an AND type, a record containing a `CardType` *and* a `CardNumber`.

```kotlin
enum class CardType { Visa, MasterCard }

data class CreditCardInfo(
    val cardType: CardType,
    val cardNumber: CardNumber,
)
```

We then define another OR type, `PaymentMethod`, as a choice between `Cash` or `Check` or `Card`. This is no longer a simple "enum" because some of the choices have data associated with them:

```kotlin
sealed interface PaymentMethod {
    object Cash: PaymentMethod

    @JvmInline
    value class Check(val number: CheckNumber): PaymentMethod

    @JvmInline
    value class Card(val info: CreditCardInfo): PaymentMethod
}
```

Define a few more basic types:

```kotlin
@JvmInline
value class PaymentAmount(val value: BigDecimal)

enum class Currency { EUR, USD }
```

And finally, the top-level `Payment` is a record containing a `PaymentAmount`, a `Currency`, and a `PaymentMethod`:

```kotlin
data class Payment(
    val amount: PaymentAmount,
    val currency: Currency,
    val method: PaymentMethod,
)
```

To document the actions that can be taken, we define types that represent functions.

So, for example, if we want to show there is a way to use a `Payment` type to pay for an unpaid invoice where the final result is a paid invoice, we could define a function type as follows:

```kotlin
typealias PayInvoice = (UnpaidInvoice, Payment) -> PaidInvoice
```

Or, to convert a payment from one currency to another:

```kotlin
typealias ConvertPaymentCurrency = (Payment, Currency) -> Payment
```

## Modeling Optional Values, Errors, and Collections

Let's discuss some common situations and how to represent them using types:

- Optional or missing values
- Errors
- Functions that return no value
- Collections

### Optional Values

### Errors

### No Value

### Lists and Collections
