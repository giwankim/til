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

Conceptually, an _extension function_ is a simple things: a function that can be called as a member of a class but is defined outside of it.

```kotlin
fun String.lastChar(): Char = this.get(this.length - 1)
```

The class of interface you're extending is called the _receiver type_, the value on which you're calling the extension function is called the _receiver object_.

In the body of an extension function, you use `this` the same way you would use it in a method. And as in a regular method, you can omit it. You can directly access the methods and properties of the class you're extending. However, unlike methods defined in the class, extension functions don't have access to private or protected members of the class.

### Imports and extension functions

When you define an extension function, it doesn't automatically become available across the entire project. Instead, it needs to be imported, just like any other class or function.

```kotlin
import strings.lastChar

val c = "Kotlin".lastChar()
```

```kotlin
import strings.*

val c = "Kotlin".lastChar()
```

```kotlin
import strings.lsatChar as last

val c = "Kotlin".last()
```

### Calling extension functions from Java

Under the hood, an extension function is a static method that accepts the receiver object as its first argument.

So to make use extension functions from Java, you call the static method and pass the receiver object instance. Let's say the extensions function was declared in a StringUtil.kt file:

```java
/* Java */
char c = StringUtilKt.lastChar("Java")
```

### Utility functions as extensions

Now, we can write the final version of the `joinToString` function.

```kotlin
fun <T> Collection<T>.joinToString(
    separator: String = ", ",
    prefix: String = "",
    postfix: String = "",
): String {
    val result = StringBuilder(prefix)

    for ((index, element) in this.withIndex()) {
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
    println(list.joinToString(separator = "; ", prefix = "(", postfix = ")"))
    // (1; 2; 3)
}
```

We can use a more specific type as a receiver type.

```kotlin
fun Collection<String>.join(
    separator: String = ", ",
    prefix: String = "",
    postfix: String = "",
) = joinToString(separator, prefix, postfix)

fun main() {
    println(listOf("one", "two", "eight").join(" "))
    // one two eight
}
```

### No overriding for extension functions

Even though you can define extension functions with the same name and parameter types for a base class and its subclass, the function that's called depends on the declared static type of the variable, determined at compile time, not the run-time type of the value stored in that variable.

### Extension properties

You can also specify _extension properties_. These allow you to extend classes with APIs that can be accessed using the property syntax. Extension properties always have to define custom accessors.

```kotlin
val String.lastChar: Char
    get() = this.get(length - 1)

var StringBuilder.lastChar: Char
    get() = this.get(length - 1)
    set(value) {
        setCharAt(length - 1, value)
    }

fun main() {
    val sb = StringBuilder("Kotlin?")
    println(sb.lastChar)
    // ?
    sb.lastChar = '!'
    println(sb)
    // Kotlin!
}
```

## Working with collections: varargs, infix calls, and library support

- The `vararg` keyword, which allows you to declare a function taking an arbitrary number of arguments
- An _infix_ notation that lets you call some one-argument functions without ceremony
- _Destructuring declarations_ that allow you to unpack a single composite value into multiple variables

### Varargs

When you call a function to create a list, you can pass any number of arguments to it:

```kotlin
val list = listOf(2, 3, 5, 7, 11)
```

If you look up how this function is declared in the standard library, you'll find the following signature:

```kotlin
fun listOf<T>(vararg values: T): List<T> { /* implementation */ }
```

Kotlin's varargs are similar to those in Java, but the syntax is slightly different.

Another difference between Kotlin and Java is the syntax of calling the function when the arguments you need to pass are already packed in an array. In Java, you pass the array as is, whereas Kotlin requires you to explicitly unpack the array so that every element becomes a separate argument to the function being called. This feature is called a _spread operator_, and to use it put the * character before the corresponding argument.

```kotlin
fun main(args: Array<String>) {
    val list = listOf("args: ", *args)
    println(list)
}
```

### Infix calls and destructuring declarations

In an infix call, the method name is places immediately between the target object name and the parameter, with no extra separators. The following two calls are equivalent:

```kotlin
1.to("one")
1 to "one"
```

Infix calls can be used with regular methods and extension functions that have exactly one required parameter. To allow a function to be called using infix notation, you need to mark it with the _infix_ modifier.

```kotlin
infix fun Any.to(other: any) = Pair(this, other)
```

Note that you can initialize two variables with the contents of a `Pair` directly:

```kotlin
val (number, name) = 1 to "one"
```

This feature is called a _destructuring declaration_.

## Working with strings and regular expressions

Kotlin makes working with standard Java string more convenient by providing several useful extension functions. Also, it hides some confusing methods, adding extensions that are clearer.

### Splitting strings

It's a common trap to write `"12.345-6.A".split(".")` and expect an array `[12, 345-6, A]` as a result. But Java's `split` methods returns an empty array. That happens because it takes a regular expression as a parameter and splits a string into several strings. Here, the dot (.) is a regular expression that denotes any character.

Kotlin hides the confusing method and provides as replacements several overloaded extensions named `split` that have different arguments. The one that takes a regular expression requires a value of type `Regex` or `Pattern`, not `String`.

```kotlin
fun main() {
    println("12.345-6.A".split("\\.|-".toRegex()))
    // [12, 345, 6, A]
}
```

```kotlin
fun main() {
    println("12.345-6.A".split(".", "-"))
    // [12, 345, 6, A]
}
```

### Regular expressions and triple-quoted strings

### Multiline triple-quoted strings

The purpose of triple-quoted strings is not only to avoid escaping characters. Such a strong literal can contain any characters, including line breaks.

By calling `trimIndent`, you can remove that common minimal indent of all the lines of your string and remove the first and last lines of the string, given they are blank.

## Local functions and extensions

You can nest functions you've extracted in the containing function.

Let's see how to use local functions to fix a fairly common case of code duplication.

```kotlin
class User(
    val id: Int,
    val name: String,
    val address: String,
)

fun saveUser(user: User) {
    if (user.name.isEmpty()) {
        throw IllegalArgumentException("Can't save user ${user.id}: empty Name")
    }

    if (user.address.isEmpty()) {
        throw IllegalArgumentException("Can't save user ${user.id}: empty Address")
    }

    // Save user to the database
}

fun main() {
    saveUser(User(1, "", ""))
    // java.lang.IllegalArgumentException: Can't save user 1: empty Name
}
```

If you put the validation code into a local function, you can get rid of the duplication.

```kotlin
fun saveUser(user: User) {
    fun validate(
        value: String,
        fieldName: String,
    ) {
        if (value.isEmpty()) {
            throw IllegalArgumentException("Can't save user ${user.id}: empty $fieldName")
        }
    }

    validate(user.name, "Name")
    validate(user.address, "Address")

    // Save user to the database
}
```

To further improve this example, you can move the validation logic into an extension function of the `User` class.

```kotlin
fun User.validateBeforeSave() {
    fun validate(
        value: String,
        fieldName: String,
    ) {
        if (value.isEmpty()) {
            throw IllegalArgumentException("Can't save user $id: empty $fieldName")
        }
    }

    validate(name, "Name")
    validate(address, "Address")
}

fun saveUser(user: User) {
    user.validateBeforeSave()

    // Save user to the database
}
```

Extracting a piece of code into an extension function turn out to be surprisingly useful. If you follow this approach, the API of the class contains only the essential methods used everywhere, so the class remains small and easy to wrap your head around. On the other hand, functions that primarily deal with a single object and don't need access to its private data can access its members without extra qualification, as in the above example.
