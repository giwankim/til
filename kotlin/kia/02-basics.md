# 2. Kotlin basics

## Functions and variables

### "Hello, world!"

```kotlin
fun main() {
  println("Hello, world!")
}
```

- `fun` keyword is used to declare a function.
- The function can be declared at the top level of any Kotlin file.
- You can specify the `main` function as the entry point for your application at the top level and without additional arguments.
- The Kotlin standard library provides many wrappers around standard Java library functions with more concise syntax, and `println` is one of them.

### Declaring functions with parameters and return values

```kotlin
fun max(a: Int, b: Int): Int {
  return if (a > b) a else b
}
```

A Kotlin function is introduced with the `fun` keyword. Parameters and their types follow in parentheses, each annotated with a name and a type, separated by a colon. Its return type is specified after the end of the parameter list.

Note that in Kotlin, `if` is an expression with a result value. You can think of `if` as returning a value from either of its branches.

### Difference between expressions and statements

The difference between an expression and a statement is that an expression has a value, which can be used as part of another expression, whereas a statement is always a top-level element in its enclosing block and doesn't have its own value.

On the other hand, Kotlin enforces assignments to always be statement.

### More concise function definition using expression bodies

```kotlin
fun max(a: Int, b: Int): Int = if (a > b) a else b
```

If a function is written with its body in curly braces, we say this function has a _block body._ If it returns an expression directly, it has an _expression body._

You can simplify the `max` function further and omit the return type:

```kotlin
fun max(a: Int, b: Int) = if (a > b) a else b
```

Omitting the return type is allowed only for functions with an expression body.

### Variable declaration

A variable declaration in Kotlin starts with a keyword (`val` or `var`), followed by the name for the variable. While Kotlin lets you omit the type for many variable declarations (thanks to its powerful `type inference`), you can always explicitly put the type after the variable name.

If you're not initializing your variable immediately, the compiler won't be able to infer the type for the variable. In this case, you need to specify its type explicitly:

```kotlin
fun main() {
  val answer: Int
  answer = 42
}
```

### Variable: read-only or reassignable

Kotlin provides two keywords `val` and `var` for declaring variables:

- `val` (from _value_) declares a _read-only reference._
- `var` (from _variable_) declares a _reassignable reference._

By default, you should strive to declare all variables in Kotlin with the `val` keyword. Using read-only references, immutable objects, and functions without side effects allows you to take advantage of the benefits offered by _functional programming_ style.

A `val` variable must be initialized exactly once during the execution of the block where it's defined. However, you can initialize it with different values depending on some condition, as long as the compiler can ensure only one of the initialization statements will be executed.

```kotlin
fun canPerformOperation(): Boolean = true

fun main() {
  val result: String
  if (canPerformOperation()) {
    result = "Success"
  } else {
    result = "Can't perform operation"
  }
}
```

Note that, even though `val` reference is itself read-only and can't be changed once it has been assigned, the object it points to may be mutable.

```kotlin
fun main() {
  val languages = mutableListOf("Java")
  languages.add("Kotlin")
}
```

Even though `var` keyword allows a variable to change its value, its type is fixed.

```kotlin
fun main() {
  var answer = 42
  answer = "no answer" // Error: type mismatch
}
```

### String formatting

Like many scripting languages, Kotlin allows you to refer to local variables in string literals by putting the `$` character in front of the variable name.

__Note__ For JVM 1.8 targets, the compiled code creates a `StringBuilder` and appends the constant parts and variable values to it. Applications targeting JVM 9 or above compile string concatenations into more efficient dynamic invocations via `invokedynamic`.

```kotlin
fun main() {
  val input = readln()
  val name = if (input.isNotBlank()) input else "Kotlin"
  println("Hello, $name!")
}
```

## Classes and properties

Like other object-oriented languages, Kotlin provides the abstraction of a _class._

```java
/* Java */
public class Person {
  private final String name;

  public Person(String name) {
    this.name = name;
  }

  public String getName() {
    return name;
  }
}
```

The same `Person` class in Kotlin.

```kotlin
class Person(val name: String)
```

Note that the modifier `public` disappeared during the conversion. In Kotlin, `public` is the default visibility, so you can omit it.

### Properties

In Java, the combinations of the field and its accessors is often referred to as a _property._ In Kotlin, properties are a first-class language feature that entirely replaces fields and accessor methods. You declare a property in a class the same way you declare a variable: with the `val` and `var` keywords.

```kotlin
class Person (
  val name: String, // read-only property--generates a field and a trivial getter
  var isStudent: Boolean //writable property--a field, getter, and setter
)
```

Basically, when you declare a property, you declare the corresponding accessors.

The concise declaration of the `Person` class hides the same underlying implementation as the original Java code: it's a class with private fields that is initialized in the constructor and can be accessed through the corresponding getter. That means you can use this class from Java and from Kotlin the same way, independent of where it was declared. Here's how you can use the Kotlin class `Person` from Java code.

```java
public class PersonUser {
  public static void main(String[] args) {
    Person person = new Person("Bob", true);
    System.out.println(person.getName());
    // Bob
    System.out.println(person.isStudent());
    // true
    person.setStudent(false);
    System.out.println(person.isStudent());
    // false
  }
}
```

You can convert the above Java code to Kotlin as follows.

```kotlin
fun main() {
  val person = Person("Bob", true)
  println(person.name)
  // Bob
  println(person.isStudent)
  // true
  person.isStudent = false
  println(person.isStudent)
  // false
}
```

#### Tip

You can also use the Kotlin property syntax for classes defined in Java. Getters in a Java class can be accessed as `val` properties from Kotlin, and getter-setter pairs can be accessed as `var` properties.

### Custom accessors

```kotlin
class Rectangle(
    val height: Int,
    val width: Int,
) {
    val isSquare: Boolean
        get() {
            return height == width
        }
}
```

Note that you can define the getter using expression-body syntax and write `val isSquare get() = height == width` as well. The expression-body syntax allows you to omit explicitly specifying the property type, letting the compiler infer the type.

To invoke the property like `isSquare`

```kotlin
fun main() {
    val rectangle = Rectangle(41, 43)
    println(rectangle.isSquare)
    // false
}
```

You might ask whether it's better to declare a property with a custom getter or define a function inside the class (referred to as a _member function_ or _method_). Generally, if you describe the characteristic (the property) of a class, you should declare it as a property. If you are describing the behavior of a class, choose a member function instead.

### Source code layout: Directories and packages

Kotlin uses the concept of _packages_ to organize classes. Every Kotlin file can have a `package` statement at the beginning, and all declarations (classes, functions, and properties) defined in the file will be placed in that package.

Declarations defined in other files can be used directly if they're in the same package; they need to be _imported_ if they're in a different package.

In Java, you put your classes into a structure of files and directories that matches the package structure.

In Kotlin, you can put multiple classes in the same file and choose any name for that file. Kotlin doesn't impose any restrictions on the layout of source files on disk.

## Enums and when

### Enum class declaration

In Kotlin, `enum` is a _soft keyword:_ it has a special meaning when it comes before `class`, but you can use it as a regular name (e.g., for a function, variable name or parameter) in other places. On the other hand, `class` is a _hard keyword_, meaning you can't use it as an identifier: you can use an alternate spelling or phrasing, like `clazz` or `aClass`.

```kotlin
package ch02.enums.colors

enum class Color(
    val r: Int,
    var g: Int,
    var b: Int,
) {
    RED(255, 0, 0),
    ORANGE(255, 165, 0),
    YELLOW(255, 255, 0),
    GREEN(0, 255, 0),
    BLUE(0, 0, 255),
    INDIGO(75, 0, 130),
    VIOLET(238, 130, 238),
    ;

    val rgb = (r * 256 + g) * 256 + b

    fun printColor() = println("$this is $rgb")
}

fun main() {
    println(Color.BLUE.rgb)
    // 255
    Color.GREEN.printColor()
    // GREEN is 65280
}
```

Note that this example shows the only place in Kotlin syntax where you're required to use semicolons: if you define any methods in the enum class, the semicolon separates the enum constant list from the method definitions.

### When expression

Like `if,` `when` is an expression that returns a value. The following is a function with an expression body, returning the `when` expression directly.

```kotlin
fun getMnemonic(color: Color) =
    when (color) {
        Color.RED -> "Richard"
        Color.ORANGE -> "Of"
        Color.YELLOW -> "York"
        Color.GREEN -> "Gave"
        Color.BLUE -> "Battle"
        Color.INDIGO -> "In"
        Color.VIOLET -> "Vain"
    }
```

You can also combine multiple values in the same branch if you separate them with commas.

```kotlin
fun getWarmthFromSensor(): String =
    when (val color = measureColor()) {
        Color.RED, Color.ORANGE, Color.YELLOW -> "warm (red = ${color.r})"
        Color.GREEN -> "neutral (green = ${color.g})"
        Color.BLUE, Color.INDIGO, Color.VIOLET -> "cold (blue = ${color.b})"
    }
```

Note that anytime `when` is used as an expression (meaning its result is used in an assignment or as a return value), the compiler enforces the construct to be _exhaustive._

The `when` expression can capture its subject in a variable.

The `when` construct in Kotlin is more flexible than in other languages--you can use any kind of object as a branch condition.

```kotlin
fun mix(
    c1: Color,
    c2: Color,
) = when (setOf(c1, c2)) {
    setOf(RED, YELLOW) -> ORANGE
    setOf(YELLOW, BLUE) -> GREEN
    setOf(BLUE, VIOLET) -> INDIGO
    else -> throw Exception("Dirty color")
}
```

Since the Kotlin compiler can't deduce that we have covered all possible combinations of color set, a default case is provided.

It is also possible to write a `when` expression without an argument.

```kotlin
fun mixOptimized(
    c1: Color,
    c2: Color,
) = when {
    (c1 == RED && c2 == YELLOW) || (c1 == YELLOW && c2 == RED) -> ORANGE
    (c1 == YELLOW && c2 == BLUE) || (c1 == BLUE && c2 == YELLOW) -> GREEN
    (c1 == BLUE && c2 == VIOLET) || (c1 == VIOLET && c2 == BLUE) -> INDIGO
    else -> throw Exception("Dirty color")
}
```

If no argument is supplied for the `when` expression, the branch condition is any Boolean expression.

### Smart casts: Combining type checks and casts

We'll write a function that evaluates simple arithmetic expression, like `(1 + 2) + 4`. In the process, we'll learn about how _smart casts_ make it much easier to work with Kotlin objects of different types.

To mark that a class implements an interface, you use a colon (:) followed by the interface name.

```kotlin
interface Expr

class Num(
    val value: Int,
) : Expr

class Sum(
    val left: Expr,
    val right: Expr,
) : Expr
```

First, we'll look an implementation of this function written in a style similar to what you might see in Java code. Then, we'll refactor it to reflect idiomatic Kotlin.

```kotlin
fun eval(e: Expr): Int {
    if (e is Num) {
        val n = e as Num
        return n.value
    }
    if (e is Sum) {
        return eval(e.left) + eval(e.right)
    }
    throw IllegalArgumentException("Unknown expression")
}
```

Kotlin's `is` check provides some additional convenience: if you check the variable for a certain type, you don't need to cast it afterward. In effect, the compiler performs the cast for you, something we call a _smart cast_.

Recall that `if` expression can already return a value.

```kotlin
fun eval(e: Expr): Int =
    if (e is Num) {
        e.value
    } else if (e is Sum) {
        eval(e.right) + eval(e.left)
    } else {
        throw IllegalArgumentException("Unknown expression")
    }
```

There is an even better language construct for expressing multiple choices.

```kotlin
fun eval(e: Expr): Int =
    when (e) {
        is Num -> e.value
        is Sum -> eval(e.left) + eval(e.right)
        else -> throw IllegalArgumentException("Unknown expression")
    }
```

Both `if` and `when` can have blocks as branches. In this case, the last expression in the block is the result.

```kotlin
fun eval(e: Expr): Int =
    when (e) {
        is Num -> {
            println("num: ${e.value}")
            e.value
        }
        is Sum -> {
            val left = eval(e.left)
            val right = eval(e.right)
            println("sum: $left + $right")
            left + right
        }
        else -> throw IllegalArgumentException("Unknown expression")
    }

fun main() {
    println(eval(Sum(Sum(Num(1), Num(2)), Num(4))))
    // num: 1
    // num: 2
    // sum: 1 + 2
    // num: 4
    // sum: 3 + 4
    // 7
}
```

The rule that _the last expression in a block is the result_ holds in all cases in which a block can be used and a result is expected.

## Iteration: while and for loops

### while loop

Kotlin has `while` and `do-while` loops.

For nested loops, Kotlin allows you to specify a _label_, which you can then reference when using `break` or `continue`. A label is an identifier followed by the at sign (@):

```kotlin
outer@ while (outerCondition) {
  while (innerCondition) {
    if (shouldExitInner) break
    if (shouldSkipInner) continue
    if (shouldExit) break@outer
    if (shouldSkip) continue@outer
  }
}
```

### Ranges and progressions

Kotlin does not have C-style `for` loop. To replace the most common use cases for such loops, Kotlin uses the concepts of `ranges`. A range is essentially just an interval between two values: a start and an end. You write it using the `..` operator:

```kotlin
val oneToTen = 1..10
```

Note that in Kotlin, these ranges are _closed_ or _inclusive_.

The most basic thing you can do with integer ranges is loop over all the values. If you can iterate over all the values in a range, such a range is called a _progression_.

```kotlin
fun main() {
  for (i in 1..100) {
    print(i)
  }
}
```

A progression can also have a _step_, which allows it to skip some numbers. The step can also be negative, in which case the progression goes backward rather than forward.

```kotlin
fun main() {
  for (i in 100 downTo 1 step 2) {
    print(i)
  }
}
```

The `..` syntax creates a range that includes the end point. To create a half-closed range, which doesn't include the specified end point, use `..<`. For example, the loop `for (x in 0..<size)` is equivalent to `for (x in 0..size-1)`.

### Iterating over maps

The most common scenario of using a `for (x in y)` loop is iterating over a collection.

Let's see an example of iterating over a map.

```kotlin
fun main() {
    val binaryReps = mutableMapOf<Char, String>()
    for (char in 'A'..'F') {
        val binary = char.code.toString(radix = 2)
        binaryReps[char] = binary
    }

    for ((letter, binary) in binaryReps) {
        println("$letter = $binary")
    }
    // A = 1000001
    // B = 1000010
    // C = 1000011
    // D = 1000100
    // E = 1000101
    // F = 1000110
}
```

You can use the same unpacking syntax to iterate over a collection while keeping track of the index of the current item.

```kotlin
fun main() {
    val list = listOf("10", "11", "1001")
    for ((index, element) in list.withIndex()) {
        println("$index: $element")
    }
    // 0: 10
    // 1: 11
    // 2: 1001
}
```

### Using `in` to check collection and range membership

The `in` operator checks whether a value is in a range or its opposite, `!in`, to check whether a value isn't in a range.

If you have any class that supports comparing instances (by implementing the `kotlin.Comparable` interface), you can create ranges of objects of that type. If you have such a range, you can't enumerate all objects in the range but can still check whether another object belongs to the range, using the `in` operator:

```kotlin
fun main() {
  println("Kotlin" in "Java".."Scala")
  // true
}
```

The same `in` check works with collections as well:

```kotlin
fun main() {
  println("Kotlin" in setOf("Java", "Scala"))
  // false
}
```

## Exceptions

Exception handling in Kotlin is similar to the way it is done in Java. A function can complete in a normal way or throw an exception if an error occurs. The function caller can catch this exception and process it; if it doesn't, the exception is re-thrown further up the stack.

You throw an exception using the `throw` keyword--in this case, to indicate that the calling function has provided an invalid percentage value:

```kotlin
if (percentage !in 0..100) {
  throw IllegalArgumentException(
    "A percentage value must be between 0 and 100: $percentage")
}
```

The `throw` construct is an _expression_ and can be used as a part of other expressions:

```kotlin
val percentage =
    if (number in 1..100) {
        number
    } else {
        throw IllegalArgumentException("A percentage value must be between 0 and 100: $number")
    }
```

### Handling exceptions and recovering from errors: try, catch, and finally

You can use the `try` construct with `catch` and `finally` clauses to handle exceptions.

```kotlin
import java.io.BufferedReader
import java.io.StringReader

fun readNumber(reader: BufferedReader): Int? {
    try {
        val line = reader.readLine()
        return Integer.parseInt(line)
    } catch (e: NumberFormatException) {
        return null
    } finally {
        reader.close()
    }
}

fun main() {
    val reader = BufferedReader(StringReader("239"))
    println(readNumber(reader))
    // 239
}
```

An important difference from Java is that Kotlin doesn't have a `throws` clause. If you wrote this function in Java, you'd explicitly write `throws IOException` after the function declaration.

`Integer readNumber(BufferedReader reader) throws IOException;`

Kotlin doesn't differentiate between checked and unchecked exceptions. You don't specify the exceptions thrown by a function, and you may or may not handle any exceptions.

### Using try as an expression

Since `try` is an _expression_, you can modify the example a little to take advantage of that and assign the value of your `try` expression to a variable.

```kotlin
fun readNumber(reader: BufferedReader) {
    val number =
        try {
            Integer.parseInt(reader.readLine())
        } catch (e: NumberFormatException) {
            return
        }
    println(number)
}

fun main() {
    val reader = BufferedReader(StringReader("not a number"))
    readNumber(reader)
}

```

It's worth pointing out that, unlike with `if`, you always need to enclose the statement body in curly braces. If the body contains multiple expressions, the value of the `try` expression as a whole is the value of the last expression.

```kotlin
fun readNumber(reader: BufferedReader) {
    val number =
        try {
            Integer.parseInt(reader.readLine())
        } catch (e: NumberFormatException) {
            null
        }

    println(number)
}

fun main() {
    val reader = BufferedReader(StringReader("not a number"))
    readNumber(reader)
    // null
}
```
