# 5. Programming with lambdas

## Lambdas with receivers: with, apply, and also

_Lambdas with receivers_ call methods of a different object in its lambda body without any additional qualifiers.

### Performing multiple operations on the same object: with

Kotlin provides a library function `with` that you can use to perform multiple operations on the same object without repeating its name.

Consider refactoring the following example.

```kotlin
fun alphabet(): String {
    val result = StringBuilder()
    for (letter in 'A'..'Z') {
        result.append(letter)
    }
    result.append("\nNow I know the alphabet!")
    return result.toString()
}

fun main() {
    println(alphabet())
    // ABCDEFGHIJKLMNOPQRSTUVWXYZ
    // Now I know the alphabet!
}
```

The `with` function converts its first argument into a _receiver_ of the lambda that's passed as a second argument.

```kotlin
fun alphabet(): String =
    with(StringBuilder()) {
        for (letter in 'A'..'Z') {
            append(letter)
        }
        append("\nNow I know the alphabet!")
        toString()
    }
```

The value `with` returns is the result of executing the lambda code. The result is the last expression in the lambda.

### Initializing and configuring objects: apply

