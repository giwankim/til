# 7. Working with nullable values

## 7.1 Nullability

_Nullability_ is a feature of the Kotlin type system.

The approach of modern languages is to convert `NullPointerException` problems from run-time errors into compile-time errors.

## 7.2 Making possibly null variables explicit with nullable types

```java
/* Java */
int strLen(String s) {
  return s.length();
}
```

Do you expect the function to be called with a `null` argument? If you don't expect it to happen, you can declare this function in Kotlin as follows:

```kotlin
fun strLen(s: String) = s.length
```

Calling `strLen` with an argument that may be `null` isn't allowed and will be flagged as an error at compile time.

You can put a question mark after any type, to indicate that the variable of this type can store `null` references.

Once you have a value of nullable type, the set of operations you can perform on it is restricted. For example, you can no longer call methods on it.

So what can you do with a value of nullable type? The most important thing is to compare it with `null`. And once you perform the comparison, the compiler remembers that and treats the value as non-nullable in the scope where the check has been performed.

## 7.3 Taking a closer look at the meaning of types

In 1976, David Parnas defined types as a set of possible values and a set of operations that can be performed on these values (["Abstract types defined as classes of variables"](https://dl.acm.org/doi/10.1145/800237.807133))

In Java, variable of type `String` can hold one of two kinds of values: an instance of the class `String` or `null`.

## 7.4 Combining null checks and method calls with the safe call operator: ?

One of the most useful tools is the _safe-call_ operator: `?.`. This operation allows you to combine a `null` check and a method call into a single operation. For example, the expression `str?.uppercase()` is equivalent to `if (str != null) str.uppercase() else null`.

Note that the result type of such an invocation is nullable.

Safe calls can be used for accessing properties as well, not just for method calls.

If you have an object graph in which multiple properties have nullable types, it's often convenient to use multiple safe calls in the same expression.

## 7.5 Providing default values in null cases with the Elvis operator (`?:`)

## 7.8 Dealing with nullable expressions: The let function
