# 3. Defining and calling functions

In this chapter, we'll look at how Kotlin improves declaring and calling functions. We'll also look into possibilities for adapting Java libraries to the Kotlin style through the use of extension functions.

To make our discussion more useful and less abstract, we'll focus on Kotlin collections, strings, and regular expressions.

## Creating collections

```kotlin
val set = setOf(1, 7, 53)
val list = listOf(1, 7, 53)
val map = mapOf(1 to "one", 7 to "seven", 53 to "fifty-three")

fun main() {
    println(set.javaClass)
    // class java.util.LinkedHashSet

    println(list.javaClass)
    // class java.util.Arrays$ArrayList

    println(map.javaClass)
    // class java.util.LinkedHashMap
}
```

As you can see, Kotlin uses the standard Java collections classes.

```kotlin
fun main() {
    val strings = listOf("first", "second", "fourteenth")

    println(strings.last())
    // fourteenth

    println(strings.shuffled())

    val numbers = setOf(1, 14, 2)
    println(numbers.sum())
    // 17
}
```

In this chapter, we'll explore in detail how this works and where all the new methods on the Java classes come from.

## Making functions easier to call

The `joinToString` function shown next appends the elements of the collection to a `StringBuilder` with a separator between them, a prefix at the beginning, and a postfix at the end.

```kotlin
fun <T> joinToString(
    collection: Collection<T>,
    separator: String,
    prefix: String,
    postfix: String,
): String {
    val result = StringBuilder(prefix)

    for ((index, element) in collection.withIndex()) {
        if (index > 0) {
            result.append(separator)
        }
        result.append(element)
    }

    result.append(postfix)
    return result.toString()
}

fun main() {
    val list = listOf(1, 2, 3)
    println(joinToString(list, "; ", "(", ")"))
    // (1; 2; 3)
}
```

### Named arguments

The first problem we'll address concerns the readability of function calls. For example, look at the following call of `joinToString`:

```kotlin
joinToString(collection, " ", " ", ".")
```

With Kotlin, you can do better:

```kotlin
joinToString(collection, separator = " ", prefix = " ", postfix = ".")
```

When calling a function written in Kotlin, you can specify the names of some (or all) of the arguments you're passing to the function. If you specify the names of all arguments passed to the function, you can even change their order:

```kotlin
joinToString(
  postfix = ".",
  separator = " ",
  collection = collection,
  prefix=""
)
```

### Default parameter values

In Kotlin, you can avoid creating overloads because you can specify default values for parameters in a function declaration.

```kotlin
fun main() {
    val list = listOf(1, 2, 3)

    println(joinToString(list, ", ", "", ""))
    // 1, 2, 3
    println(joinToString(list))
    // 1, 2, 3
    println(joinToString(list, "; "))
    // 1; 2; 3

    println(joinToString(collection = list, separator = "; ", prefix = "(", postfix = ")"))
    // (1; 2; 3)
}
```

### Top-level functions and properties

In Kotlin, you can place functions directly at the top of a source file, outside of any class.

Let's put `joinToString` function into the `strings` package directly. Create a file called join.kt with the following contents.

```kotlin
package strings

fun joinToString( /* ... */ ): String { /* ... */ }
```

If you need to call such a function from Java, you have to understand how it will be compiled. Let's looks at the Java code that would compile to the same class:

```java
package strings;

public class JoinKt {
  public static String joinToString( /* ... */ )  { /* ... */ }
}
```

#### Top-level properties

The value of top-level properties will be stored in a static field.

By default, top-level properties, just like any other properties, are exposed to Java code as accessor methods (a getter for a `val` property and a getter-setter pair for a `var` property).

If you want to expose a constant to Java code as a `public static final` field, to make its use more natural, you can mark it with the `const` modifier:

```kotlin
const val UNIX_LINE_SEPARATOR = "\n"
```

This gets you the equivalent of the following Java code:

```java
/* Java */
public static final String UNIX_LINE_SEPARATOR = "\n";
```

## Extension functions and properties

## Working with collections: varargs, infix calls, and library support

## Working with strings and regular expressions

## Local functions and extensions
