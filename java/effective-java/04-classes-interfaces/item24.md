# Item 24: Favor static member classes over nonstatic

A _nested class_ is a class defined within another class. There are four kinds of nested classes: _static member classes_, _nonstatic member classes_, _anonymous classes_, and _local classes_.

## Static member class

A static member class is best thought of as an ordinary class that happens to be declared inside another class and has access to all of the enclosing class's members, even those declared private. A static member class is a static member of its enclosing class and obeys the same accessibility rules as other static members.

One common use of a static member class is as a public helper class, useful only in conjunction with its outer class.

## Nonstatic member class

Each instance of a nonstatic member class is implicitly associated with an _enclosing instance_ of its containing class. Within instance methods of a nonstatic member class, you can invoke methods on the enclosing instance or obtain a reference to the enclosing instance using the _qualified this_ construct. If an instance of a nested class can exist in isolation from an instance of its enclosing class, then the nested class _must_ be a static member class: it is impossible to create an instance of a nonstatic member class without an enclosing instance.

One common use of a nonstatic member class is to define an _Adapter_ that allows an instance of the outer class to be viewed as an instance of some unrelated class. For example, implementations of the `Map` interface typically use nonstatic member classes to implement their _collection views_, which are returned by `Map`'s `keySet`, `entrySet`, and `values` methods. Similarly, implementations of the collection interfaces, such as `Set` and `List`, typically use nonstatic member classes to implement their iterators:

```java
// typical use of nonstatic member class
public class MySet<E> extends AbstractSet<E> {
  ... // Bulk of the class omitted

  @Override public Iterator<E> iterator() {
    return new MyIterator();
  }

  private class MyIterator implements Iterator<E> {
    ...
  }
}
```

## Static vs Nonstatic member class

__If you declare a member class that does not require access to an enclosing instance, _always_ put the `static` modifier in its declaration.__ If you omit this modifier, each instance will have a hidden extraneous reference to its enclosing instance. It can result in the enclosing instance being retained when it would otherwise be eligible for garbage collection.

A common use of private static member classes is to represent components of the object represented by their enclosing class. For example, consider a `Map` instance, which associates keys with values. Many `Map` implementations have an internal `Entry` object for each key-value pair in the map. While each entry is associated with a map, the methods on an entry (`getKey`, `getValue`, and `setValue`) do not need access to the map. Therefore, it would be wasteful to use a nonstatic member class to represent entries: a private static member class is best.

It is doubly important to choose correctly between a static and a nonstatic member class if the class in question is a public or protected member of an exported class. In this case, the member class is an exported API element and cannot be changed from a nonstatic to a static member class in a subsequent release without violating backward compatibility.

## Anonymous class

An anonymous class has no name. It is not a member of its enclosing class. It is simultaneously declared and instantiated at the point of use. Anonymous classes have enclosing instances if and only if they occur in a nonstatic context. But even if they occur in a static context, they cannot have any static members other than _constant variables_, which are final primitive or string fields initialized to constant expressions.

Before lambdas were added to Java, anonymous classes were the preferred means of creating small _function objects_ and _process objects_ on the fly, but lambdas are now preferred. Another common use of anonymous classes is in the implementation of static factory methods:

```java
// Concrete implementation built atop skeletal implementation
static List<Integer> intArrayAsList(int[] a) {
  Objects.requireNonNull(a);

  return new AbstractList<>() {
    @Override public Integer get(int i) {
      return a[i];
    }

    @Override public Integer set(int i, Integer val) {
      int oldVal = a[i];
      a[i] = val;
      return oldVal;
    }

    @Override public int size() {
      return a.length;
    }
  }
}
```

## Local class

A local class can be declared practically anywhere a local variable can be declared and obeys the same scoping rules. Like member classes, they have names and can be used repeatedly. Like anonymous classes, they have enclosing instances only if they are defined in a nonstatic context, and they cannot contain static members. And like anonymous classes, they should be kept short so as not to harm readability.

## Recap

There are four different kinds of nested classes, and each has its place. If a nested class needs to be visible outside of a single method or is too long to fit comfortably inside a method, use a member class. If each instance of a member class needs a reference to its enclosing instance, make it nonstatic; otherwise, make it static. Assuming the class belongs inside a method, if you need to create instances from only one location and there is a preexisting type that characterizes the class, make it an anonymous class; otherwise, make it a local class.
