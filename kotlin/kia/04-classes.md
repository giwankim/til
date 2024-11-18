# 4. Classes, objects, and interfaces

Kotlin's classes and interfaces differ a bit from Java: for example, interfaces can contain property declarations. Kotlin's declarations are `final` and `public`, by default. In addition, nested classes aren't inner by default.

For constructors, the short primary constructor syntax works great for the majority of cases, but Kotlin also comes with full syntax that lets you declare constructors with nontrivial initialization logic. The same works for properties: you can define your implementation of accessors.

Kotlin compiler can generate useful methods to avoid verbosity. Declaring a class as a `data` class instructs the compiler to generate several standard methods for this class. You can also avoid writing delegating methods by hand.

You'll also get to see Kotlin's `object` keyword, which declares a class and also creates an instance of the class. The keyword is used to express singleton objects, companion objects, and object expressions.

## Defining class hierarchies

We'll take a look at how class hierarchies are defined in Kotlin. We'll look at Kotlin's visibility and access modifiers as well as which defaults Kotlin chooses for them. We'll also learn about the `sealed` modifier, which restricts the possible subclasses of a class or implementations of an interface.

### Interfaces

Kotlin interfaces can contain definitions of abstract methods as well as implementation non-abstract methods; however, they can't contain any state.

To declare an interface in Kotlin, use the `interface` keyword instead of `class`.

```kotlin
interface Clickable {
  fun click()
}
```

This declares an interface with a single abstract method named `click`, which doesn't return any value.

To mark a button as `Clickable`, you put the interface name behind a colon after the class name and provide an implementation for the `click` function.

```kotlin
class Button : Clickable {
    override fun click() = println("I was clicked")
}
```

Kotlin uses the colon after the class name for both _composition_ and _inheritance_. A class can implement as many interfaces as it wants, but it can extend only one class.

The `override` modifier is used to mark methods and properties that override those from the superclass or interface. Unlike Java, using `override` modifier is _mandatory_ in Kotlin.

An interface method can have a default implementation. To do so, you just provide a method body.

```kotlin
interface Clickable {
    fun click()

    fun showOff() = println("I'm clickable!")
}
```

If you implement this interface, you are forced to provide an implementation for `click`. You can redefine the behavior of the `showOff` method.

Let's suppose another interface also defines a `showOff` method and has the following implementation for it.

```kotlin
interface Focusable {
    fun setFocus(b: Boolean) = println("I ${if (b) "got" else "lost"} focus.")

    fun showOff() = println("I'm focusable!")
}
```

What happens if you need to implement both interfaces in your class? You get the following compiler error:

```java
The class 'Button' must override public open fun showOff()
because it inherits many implementations of it.
```

Kotlin compiler forces you to provide your own implementation.

```kotlin
class Button :
    Clickable,
    Focusable {
    override fun click() = println("I was clicked")

    override fun showOff() {
        super<Clickable>.showOff()
        super<Clickable>.showOff()
    }
}

fun main() {
    val button = Button()
    button.showOff()
    // I'm clickable!
    // I'm focusable!
    button.setFocus(true)
    // I got focus
    button.click()
    // I was clicked
}
```

### Open, final, and abstract modifiers: Final by default

By default, you can't create a subclass for a Kotlin class or override any methods from a base case--all classes and methods are `final` by default.

The _fragile base class_ problem occurs when modifications of a base class can cause incorrect behavior of subclasses because the changed code of the base class no longer matches the assumption in its subclasses. Because it's impossible to analyze all the subclasses, the base class is "fragile."

If you want to allow the creation of subclasses of a class, you need to mark the class with the `open` modifier. In addition, you need to add the `open` modifier to every property or method that can be overridden.

Let's say you want to create a clickable `RickButton`. You could declare the class as follow.

```kotlin
open class RichButton : Clickable { // This class is open: others can inherit from it.
  fun disable() { /* ,,, */ } // This function is final: you can't override it in a subclass.
  open fun animate() { /* ,,, */ } // This function is open: you may override it in  a subclass.
  override fun click() { /* ,,, */ } // This function overrides an open function and is open as well.
}
```

This means a subclass of `RichButton` could, in turn, look like the following.

```kotlin
class ThemedButton : RichButton() { // Because disable is final in RichButton by default, you can't override it here.
  override fun animate() { /* ,,, */ } // animate is explicitly open, so you can override it.
  override fun click() { /* ,,, */ } // You can override click because RichButton didn't explicitly mark it as final.
  override fun showOff() { /* ,,, */ } // You can override showOff even though RichButton didn't provide an override.
}
```

Note that if you override a member of a base class or interface, the overriding member will also be `open` by default. If you want to change this and forbid the subclasses from overriding your implementation, you can explicitly mark the overridden member as `final`.

```kotlin
open class RichButton : Clickable {
  final override fun click() { /* ,,, */ } // final
}
```

You can also declare a class as `abstract`, making it so the class can't be instantiated. An abstract class usually contains abstract members that don't have implementations and must be overridden in subclasses.

An example of an abstract class is a class that defines the properties of an animation, like the animation speed and number of frames, as well as behavior for running the animation. Since these properties and methods only make sense when implemented by another object, `Animated` is marked as `abstract`.

```abstract class Animated { // This class is abstract: you can't create an instance of it.
  abstract val animationSpeed: Double // This property is abstract: it doesn't have a value, and subclasses need to override its value or accessor.
  val keyframes: Int = 20 // Properties in abstract classes aren't open by
  open val frames: Int = 60 // default but can be explicitly marked as open.

  abstract fun animate()  // This function is abstract: must be overridden in subclasses.
  open fun stopAnimating() { /* ... */ } // Non-abstract functions in abstract classes
  fun animateTwice() { /* ... */ } // aren't open by default but can be marked as such.
}
```

In interfaces you don't use `final`, `open`, or `abstract`. A member in an interface is always `open`; you can't declare it as `final`. It's `abstract` if it has no body but the keyword isn't required.

### Visibility modifiers: Public by default

Visibility modifiers help to control access to declarations in your code base. By restricting the visibility of a class's implementation details, you ensure that you can change them without risk of breaking code that depends on the class.

Kotlin provides `public`, `protected`, and `private` modifiers: `public` declarations are visible everywhere; `protected` declarations are visible in subclasses; and `private` declarations are visible inside a class or, in the case of top-level declarations, visible inside a file. In Kotlin, not specifying a modifier means the declaration is `public`.

For restricting visibility inside a module, Kotlin provides the visibility modifier `internal`. A _module_ is a set of Kotlin files compiled together. This could be a Gradle source set, a Maven project, or an IntelliJ IDEA modules.

> [!NOTE]
> No package private in Kotlin
> Kotlin uses packages only as a way of organizing code in namespaces; it doesn't use them for visibility control.
> The advantage of `internal` visibility is that it provides real encapsulation. In Java, the encapsulation can be easily broken because external code can define classes in the same packages as used by your code, and thus, gain access to your package-private declarations.

Every line in the `giveSpeech` function tries to violate the visibility rules.

```kotlin
internal open class TalkativeButton {
  private fun yell() = println("Hey!")
  protected fun whisper() = println("Let's talk!")
}

fun TalkativeButton.giveSpeech() { // Error: public member exposes its internal receiver type TalkativeButton
  yell() // Error: Cannot access yell; it is private in TalkativeButton

  whisper() // Error: Cannot access whisper; it is protected in TalkativeButton
}
```

Note the difference in behavior for the `protected` modifier in Java and Kotlin. In Java, you can access `protected` member from the same package, but Kotlin doesn't allow that.

A `protected` member is _only_ visible in the class and its subclasses. Also note that extension functions of a class don't get access to its `private` and `protected` members.

Another difference in visibility rules between Kotlin and Java is that an outer class doesn't see `private` members of its inner (or nested) classes in Kotlin.

### Inner and nested classes: Nested by default

If you want to encapsulate a helper class or keep code close to where it it used, you can declare a class inside another class. Unlike in Java, nested classes in Kotlin don't have access to the outer class instance, unless you specifically request that.

Imagine we want to define a `View` element, the state of which can be serialized. We declare a `State` interface that implements `Serializable`. The `View` interface methods that can be used to save the state of a view.

```kotlin
interface State : Serializable

interface View {
    fun getCurrentState(): State

    fun restoreState(state: State) { /* ... */ }
}
```

It's handy to define a class that saves a button state in the `Button` class. Let's see how it can be done in Java.

```java
public class Button implements View {
  @Override
  public State getCurrentState() {
    return new ButtonState();
  }

  @Override
  public void restoreState(State state) { /* ... */ }

  public class ButtonState implements State { /* ... */ }
}
```

What's wrong with this code? Why do you get a `java.io.NotSerializableException:Button` exception if you try to serialize the state of the declared button?

Recall in Java, when you declare a class in another class, it becomes an inner class by default. The `ButtonState` class implicitly stores a reference to its outer `Button` class. That explains why `ButtonState` can't be serialized: `Button` isn't serializable, and the reference to it breaks the serialization of `ButtonState`.

To fix this problem, you need to declare the `ButtonState` class as `static`. Declaring a nested class as `static` removes the implicit reference from that class to its enclosing class.

In Kotlin, the default behavior of inner classes is the opposite of what we've just described.

```kotlin
class Button : View {
    override fun getCurrentState(): State = ButtonState()

    override fun restoreState(state: State) { /* ... */ }

    class ButtonState : State
}
```

A nested class in Kotlin with no explicit modifiers is the same as a `static` nested class in Java. To turn it into an inner class so that it contains a reference to an outer class, you use the `inner` modifier.

| Class A declared within another class B | In Java | In Kotlin |
| --- | --- | --- |
| Nested class (doesn't store a reference to an outer class) | `static class A` | `class A` |
| Inner class (stores a reference to an outer class) | `class A` | `inner class A` |

The syntax to reference an instance of an outer class in Kotlin also differs from Java. You write `this@Outer` to access the `Outer` class from the `Inner` class:

```kotlin
class Outer {
    inner class Inner {
        fun getOuterReference(): Outer = this@Outer
    }
}
```

### Sealed classes: Defining restricted class hierarchies

Recall the following example:

```kotlin
interface Expr
class Num(val value: Int) : Expr
class Sum(val left: Expr, val right: Expr) : Expr

fun eval(e: Expr): Int =
  when (e) {
    is Num -> e.value
    is Sum -> eval(e.left) + eval(e.right)
    else -> throw IllegalArgumentException("Unknown expression")
  }
```

It's convenient to handle all the possible cases in a `when` expression. But you must provide the `else` branch to specify what should happen if none of the other branches match.

Always having to add a default branch isn't convenient. This can also become a problem, since if you add a new subclass, the compiler won't alert you that you're missing a case. If you forget to add a branch to handle that new subclass, it will simply choose the default branch, which can lead to subtle bugs.

Kotlin comes with a solution to this problem: `sealed` classes. You mark a superclass with the `sealed` modifier, which restricts the possibility of creating subclasses. All _direct_ subclasses of a sealed class must be known at compile time and declared in the same package as the sealed class itself, and _all_ subclasses needed to be located within the same module.

```kotlin
sealed class Expr

class Num(
    val value: Int,
) : Expr()

class Sum(
    val left: Expr,
    val right: Expr,
) : Expr()

fun eval(e: Expr): Int =
    when (e) {
        is Num -> e.value
        is Sum -> eval(e.left) + eval(e.right)
    }
```

Note that the `sealed` modifier implies that the class is abstract; you don't need an explicit `abstract` modifier and can declare abstract members.

Sealed interfaces follow the same rules: once the module that contains the sealed interface is compiled, no new implementations for it can be provided.

## Declaring a class with nontrivial constructors or properties

In object-oriented languages, classes can typically have one or more constructors. Kotlin is the same, but it makes an important, explicit distinction: it differentiates between a _primary_ constructor and a _secondary_ constructor. It also allows you to put additional initialization logic in _initializer blocks_.

### Initializing classes: Primary constructor and initializer blocks

We can declare a simple class as follows:

```kotlin
class User(val nickname: String)
```

The block of code surrounded by parentheses is called a _primary constructor_. It serves two purposes:

- specifying constructor parameters and
- defining properties that are initialized by those parameters.
  
The most explicit code we can write that does the same thing is

```kotlin
class User constructor(_nickname: String) {
  val nickname: String

  init {
    nickname = _nickname
  }
}
```

The `constructor` keyword begins the declaration of a primary or secondary constructor, and the `init` keyword introduces an _initializer block_. Such blocks contain initialization code that's executed when the class is created and are intended to be used together with primary constructors.

The initialization code in the initializer block can be combined with the declaration of the `nickname` property. You can also omit the `constructor` keyword if there are no annotations or visibility modifiers on the primary constructor.

```kotlin
class User(_nickname: String) {
  val nickname = _nickname
}
```

If the property is initialized with the corresponding constructor parameter, the code can be simplified by adding the `val` keyword before the parameter. This replaces the property definition in the class body:

```kotlin
class User(val nickname: String)
```

You can declare default values for constructor parameters just as you can for function parameters:

```kotlin
class User(
    val nickname: String,
    val isSubscribed: Boolean = true,
)

fun main() {
    val alice = User("Alice")
    println(alice.isSubscribed)
    // true
    val bob = User("Bob", false)
    println(bob.isSubscribed)
    // false
    val carol = User("Carol", isSubscribed = false)
    println(carol.isSubscribed)
    // false
}
```

If the constructor of a superclass takes arguments, then the primary constructor of your class also needs to initialize them.

```kotlin
open class User(val nickname: String) { /* ... */ }

class SocialUser(nickname: String) : User(nickname) { /* ... */ }
```

If you don't declare any constructors for a class, a default constructor without parameters that does nothing will be generated for you:

```kotlin
open class Button
```

```kotlin
class RadioButton: Button()
```

If you want to ensure that your class can't be instantiated by code outside the class itself, you have to make the constructor `private`.

```kotlin
class Secret private constructor(private val agentName: String) {}
```

> [!NOTE]
> Alternative to private constructors
> In Java, you can use a `private` constructor that prohibits class instantiation to express a more general idea: the class is a container of static utility members or is a singleton. Kotlin has built-in language features for these purposes. You use top-level functions as static utilities. To express singletons, you use object declarations.

### Secondary constructors: Initializing the superclass in different ways

Take, for example, a `Downloader` class that's declared in Java and has two constructors:

```java
import java.net.URI;

public class Downloader {
  public Downloader(String url) { /* ... */ }

  public Downloader(URI uri) { /* ... */ }
}
```

In Kotlin, the same declaration would look as follows:

```kotlin
open class Downloader {
  constructor(url: String?) { /* ... */ }

  constructor(uri: URI?) { /* ... */ }
}
```

This class doesn't declare primary constructor, but it declares two secondary constructors. A secondary constructor is introduced using the `constructor` keyword.

If you want to extend this class, you can declare the same constructors:

```kotlin
class MyDownloader : Downloader {
  constructor(url: String?): super(url) { /* ... */ }

  constructor(uri: URI?): super(url) { /* ... */ }
}
```

Just as in Java, you also have an option to call another constructor of your own class from a constructor, using the `this()` keyword.

```kotlin
class MyDownloader : Downloader {
  constructor(url: String?) : this(URI(url))
  constructor(uri: URI?) : super(uri)
}
```

If the class has no primary constructor, then each secondary constructor must initialize the base class or delegate to another constructor that does so.

### Implementing properties declared in interfaces

### Accessing a backing field from a getter or setter

### Changing accessor visibility

## Compiler-generated methods: Data classes and class delegation

## The `object` keyword: Declaring a class and creating an instance, combined

## Extra type safety without overhead: Inline classes

```kotlin
class UsdCent(val amount: Int)

fun addExpense(expense: UsdCent) {
  // save the expense as USD cent
}

fun main() {
  addExpense(UsdCEnt(147))
}
```

While this approach makes it less likely to accidentally pass a value with the wrong semantics to the function, it comes with a few performance considerations: a new `UsdCent` object needs to be created with each `addExpense` function call, which is then unwrapped inside the function body and discarded.

This is where _inline classes_ come into play. They allow you to introduce a layer of type safety without compromising performance.

To turn the `UsdCent` class into an inline class, mark it with the `value` keyword, and then annotate it with `@JvmInline`:

```kotlin
@JvmInline
value class UsdCent(val amount: Int)
```

This small change avoids the needless instantiation of objects without giving up on the type safety provided by your `UsdCent` wrapper type.

To qualify as "inline", your class must have exactly one property, which needs to be instantiated in the primary constructor.
