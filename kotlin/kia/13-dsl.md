# 13. DSL construction

## From APIs to DSLs: Creating expressive custom code structures

Distinction between _general-purpose programming language_, with a set of capabilities complete enough to solve essentially any problem that can be solved with a computer, and a _domain-specific language_, which focuses on a specific task, or _domain_ and foregoes the functionality that's irrelevant for that domain.

As opposed to _external DSLs_, which have their own independent syntax, _internal DSLs_ are part of programs written in a general-purpose language.

One trait comes up often in DSLs and usually doesn't exist in other APIs: _structure_ or _grammar_. Method calls in a DSL exist in a larger structure, defined by the _grammar_ of the DSL. In a Kotlin DSL, structure is most commonly created through nesting of lambdas or through chained method calls.

## Building structured APIs: Lambdas with receivers in DSLs

We had a brief encounter with the idea of lambdas with receivers when we talked about the `buildList`, `buildString`, `with`, and `apply` standard library functions.

Let's define the `buildString` function so that it takes a regular lambda as an argument.

```kotlin
fun buildString(builderAction: (StringBuilder) -> Unit): String {
    val sb = StringBuilder()
    builderAction(sb)
    return sb.toString()
}

fun main() {
    val s =
        buildString {
            it.append("Hello, ")
            it.append("World!")
        }
    println(s)
    // Hello, World!
}
```

The main purpose of the lambda is to fill the `StringBuilder` with text, so you want to get rid of the repeated `it` prefixes and invoke the `StringBuilder` methods directly.

To do so, you need to convert the lambda into a _lambda with a receiver_. In effect, you can give one of the parameters of the lambda the special status of a _receiver_.

```kotlin
fun buildString(builderAction: StringBuilder.() -> Unit): String {
    val sb = StringBuilder()
    sb.builderAction()
    return sb.toString()
}

fun main() {
    val s =
        buildString {
            this.append("Hello, ")
            append("World!")
        }
    println(s)
    // Hello, World!
}
```

Let's discuss how the declaration of the `buildString` function has changed. We use an _extension function type_ instead of a regular function type to declare the parameter type. We replace `(StringBuilder) -> Unit` with `StringBuilder.() -> Unit`. This special type is called the _receiver type_, and the value of that type passed to the lambda becomes the _receiver object_.

The implementation of `buildString` in the standard library is shorter.

```kotlin
fun buildString(builderAction: StringBuilder.() -> Unit): String =
  StringBuilder().apply(builderAction).toString()
```

```kotlin
inline fun <T> T.apply(block: T.() -> Unit): T {
  // equivalent to this.block()
  block()
  return this
}
```

```kotlin
inline fun <T, R> with(receiver: T, block: T.() -> R): R =
  receiver.block()
```

Tha main difference is that `apply` returns the receiver itself, but `with` returns the result of calling the lambda.
