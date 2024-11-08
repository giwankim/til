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

Kotlin provides `public`, `protected`, and `private` modifiers. In Kotlin, not specifying a modifier means the declaration is `public`.

For restricting visibility inside a module, Kotlin provides the visibility modifier `internal`. A _module_ is a set of Kotlin files compiled together. This could be a Gradle source set, a Maven project, or an IntelliJ IDEA modules.

> [!NOTE]
> No package private in Kotlin
> Kotlin uses packages only as a way of organizing code in namespaces; it doesn't use them for visibility control.
> The advantage of `internal` visibility is that it provides real encapsulation. In Java, the encapsulation can be easily broken because external code can define classes in the same packages as used by your code.

### Inner and nested classes: Nested by default

### Sealed classes: Defining restricted class hierarchies

## Declaring a class with nontrivial constructors or properties

## Compiler-generated methods: Data classes and class delegation

## The `object` keyword: Declaring a class and creating an instance, combined

## Extra type safety without overhead: Inline classes
