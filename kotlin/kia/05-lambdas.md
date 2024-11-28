# 5. Programming with lambdas

## Lambdas with receivers: with, apply, and also

_Lambdas with receivers_ are lambdas that allow you to directly call methods on a special receiver object.

The `with` standard library function allows you to call multiple methods on the same object without repeating the reference to the object. `apply` lets you construct and initialize any object, using a builder-style API. `also` lets you perform additional actions with an object.

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

The `apply` function works almost exactly the same as `with`; the main difference is that `apply` always returns the object passed to it as an argument.

```kotlin
fun alphabet() =
    StringBuilder()
        .apply {
            for (letter in 'A'..'Z') {
                append(letter)
            }
            append("\nNow I know the alphabet!")
        }.toString()
```

One of many cases in which this is useful is when you're creating an instance of an object and need to initialize some properties right away.

More specific functions can also use the same pattern. For example, you can simplify the `alphabet` function even further by using the `buildString` standard library function, which will take care of creating a `StringBuilder` and calling `toString`.

```kotlin
fun alphabet() =
    buildString {
        for (letter in 'A'..'Z') {
            append(letter)
            append("\nNow I know the alphabet!")
        }
    }
```

The Kotlin standard library also comes with collection builder functions, which help you create a read-only, `List`, `Set`, or `Map`, while allowing you to treat the collection as mutable during the construction phase.

```kotlin
val fibonacci =
    buildList {
        addAll(listOf(1, 1, 2))
        add(3)
        add(index = 0, element = 3)
    }

val shouldAdd = true

val fruits =
    buildSet {
        add("Apple")
        if (shouldAdd) {
            addAll(listOf("Apple", "Banana", "Cherry"))
        }
    }

val medals =
    buildMap {
        put("Gold", 1)
        putAll(listOf("Silver" to 2, "Bronze" to 3))
    }
```

### Performing additional actions with an object: also

Just like `apply`, you can use the `also` function to take a receiver object, perform an action on it, and then return the receiver object. The main difference is that within the lambda of `also`, you access the receiver object as an argument--either by giving it a name, or using the default name `it`.

```kotlin
fun main() {
    val fruits = listOf("Apple", "Banana", "Cherry")
    val uppercaseFruits = mutableListOf<String>()
    val reversedLongFruits =
        fruits
            .map { it.uppercase() }
            .also { uppercaseFruits.addAll(it) }
            .filter { it.length > 5 }
            .also { println(it) }
            .reversed()
    // [BANANA, CHERRY]
    println(uppercaseFruits)
    // [APPLE, BANANA, CHERRY]
    println(reversedLongFruits)
    // [CHERRY, BANANA]
}
```
