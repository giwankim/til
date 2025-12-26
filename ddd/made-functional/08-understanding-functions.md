# 8. Understanding Functions

## Functions, Functions, Everywhere

Let's look at why FP is different from OOP.

In most modern languages, functions are first-class objects. The key thing about the FP paradigm is that functions are used *everywhere*, for *everything*.

## Functions are Things

Functions are things in their own right. And if functions are things, they can be passed as input to other functions. Or they can be returned as the output of a function. Or they can be passed as a parameter to a function to control its behavior.

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

// Curried  - returns a function
fun addCurried(a: Int): (Int) -> Int = { b -> a + b }

// Usage
val add3: (Int) -> Int
val add3 = addCurried(3)
```

### Partial Application
