# 2. Kotlin basics

## Functions and variables

### "Hello, world!"

```kotlin
fun main() {
  println("Hello, world!)
}
```

- `fun` keyword is used to declare a function.
- The function can be declared at the top level of any Kotlin file.
- You can specify the `main` function as the entry point for your application at the top level and without additional arguments.
- The Kotlin standard library provides many wrappers around standard Java library functions with more concise syntax, and `println` is one of them.

### Declaring functions with parameters and return values

```kotlin
fun max(a: Int, b: Int): Int {
  return if (a > b) else b
}
```

A Kotlin function is introduced with the `fun` keyword. Parameters and their types follow in parentheses, each annotated with a name and a type, separated by a colon. Its return type is specified after the end of the parameter list.

Note that in Kotlin, `if` is an expression with a result value. You can think of `if` as returning a value from either of its branches.

### Difference between expressions and statements

The difference between an expression and a statement is that an expression has a value, which can be used as part of another expression, whereas a statement is always a top-level elements in its enclosing block and doesn't have its own value.

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

A variable declaration in Kotlin starts with a keyword (`val` or `var`), followed bt the name for the variable. While Kotlin lets you omit the type for many variable declarations (thanks to its powerful `type inference`), you can always explicitly put the type after the variable name.

If you're not initializing your variable immediately, the compiler won't be able to infer the type for the variable. In this case, you need to specify its type explicitly:

```kotlin
fun main() {
  val answer: Int
  answer = 42
}
```

### Variable: read-only or reassignable

Kotlin provides tow keywords `val` and `var` for declaring variables:

- `val` (from _value_) declares _read-only reference._
- `var` (from _variable_) declares a _reassignable reference._

By default, you should strive to declare all variable in Kotlin with the `val` keyword. Using read-only references, immutable objects, and functions without side effects allows you to tak advantage of the benefits offered by _functional programming_ style.

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

Note that, even though `val` reference is itself read-only and can't be changed once it has been assigned, the object it points to may by mutable.

```kotlin
fun main() {
  val languages = mutableListOf("Java")
  languages.add("Kotlin")
}
```

Even though `var` keyword allows variable to change its value, it type is fixed.

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

The concise declaration of the `Person` class hides the same underlying implementation as the original Java code: it's a class with private fields that is initialized in the constructor and cn be accessed through the corresponding getter. That means you can use this class from Java and from Kotlin the same way, independent of where it was declared. Here's how you can use the Kotlin class `Person` from Java code.

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

You can convert to above Java code to Kotlin as follows.

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

You can also use the Kotlin property syntax for class defined in Java. Getters in a Java class can be accessed as `val` properties from Kotlin, and getter-setter pairs can be accessed as `var` properties.

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

Note that you can define the getter using expression-body syntax and write `val isSquare get() = height == width` as well. The expression-body syntax allows you to omit explicitly specifying the property type, having the compiler infer the type.

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

## Enums and when

## While and for loops

## Exceptions
