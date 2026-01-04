# 8. Understanding Functions

## Functions, Functions, Everywhere

Let's look at why FP is different from OOP.

In most modern languages, functions are first-class objects. The key thing about the FP paradigm is that functions are used *everywhere*, for *everything*.

## Functions are Things

Functions are things in their own right. And if functions are things, they can be passed as inputs to other functions. Or they can be returned as the output of a function. Or they can be passed as a parameter to a function to control its behavior.

> [!NOTE]
> Functions that input or output other functions or take functions as parameters are called *higher-order functions*.

### Treating Functions as Things

### Functions as Input

### Functions as Output

### Currying

Using this trick of returning functions, *any* multiparameter function can be converted into a series of one-parameter functions. This method is called *currying*.

Kotlin does not support currying natively, unlike F# or TypeScript, where functions are curried by default. However, you can implement currying manually:

```kotlin
// Regular function
fun add(a: Int, b: Int): Int = a + b

// Curried - returns a function
fun addCurried(a: Int): (Int) -> Int = { b -> a + b }

// Usage
val add3: (Int) -> Int
val add3 = addCurried(3)
```

### Partial Application

If every function is curried, that means you can take any multiparameter function and pass in just one argument, and get back a new function with that parameter baked in.

```kotlin
fun sayGreeting(greeting: String, name: String) {
    println("$greeting $name")
}
```

Create some new functions with the greeting baked in:

```kotlin
fun sayHello(name: String) = sayGreeting("Hello", name)
fun sayGoodbye(name: String) = sayGreeting("Goodbye", name)
```

This approach of "baking in" parameters is called *partial application* and is a very important functional pattern.

## Total Functions

A mathematical function links each possible input to an output. In FP, we try to design our functions the same way so that every input has a corresponding output. These kinds of functions are called *total functions*.

Let's demonstrate the concept with `twelveDividedBy`, which returns the result of 12 divided by the input using integer division. In pseudocode, we could implement this with a table of cases, like this:

```kotlin
fun twelveDividedBy(n: Int): Int =
    when(n) {
        6 -> 2
        5 -> 2
        4 -> 3
        3 -> 4
        2 -> 6
        1 -> 12
        0 -> ???
        else -> ???
    }
```

If we didn't care that every possible input had a corresponding output, we could just throw an exception for the zero case:

```kotlin
fun twelveDividedBy(n: Int): Int =
    when(n) {
        6 -> 2
        ...
        0 -> throw ArithmeticException("Cannot divide by zero")
        else -> ...
    }
```

But let's look at the signature of the function defined this way:

```kotlin
val twelveDividedBy: (Int) -> Int
```

This signature implies that you can pass in an `Int` and get an `Int`. But that is a lie! Sometimes you get an exception and that is not made explicit in the type signature.

One technique is to *restrict the input* to eliminate values that are illegal. For this example, we could create a new constrained type `NonZeroInteger` and pass that in.

```kotlin
fun twelveDividedBy(n: NonZeroInteger) =
    when(n) {
        6 -> 2
        ...
        // 0 can't be in the input
        // so it doesn't need to be handled
    }
```

Another technique is to *extend the output*.

```kotlin
fun twelveDividedBy(n: Int): Int? =
    when(n) {
        6 -> 2
        ...
        0 -> null
        else -> ...
    }
```

In both variants, the function signatures are explicit about what all the possible inputs and outputs are.

## Composition
