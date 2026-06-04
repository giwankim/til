# 10. Implementation: Working with Errors

We'll explore the functional approach to error handling.

## 10.1 Using the `Either` Type to Make Errors Explicit

We want a *total function* where all possible outcomes are documented explicitly by the type signature.

```kotlin
sealed interface AddressValidationError
object InvalidFormat: AddressValidationError
object AddressNotFound: AddressValidationError

typealias CheckAddressExists = UnvalidatedAddress.() -> Either<AddressValidationError, CheckedAddress>
```

## 10.2 Working with Domain Errors

Classify errors into three groups:

- *Domain Errors.* These are errors that are to be expected as part of the business process and therefore must be included in the design of the domain.
- *Panics.* These are errors that leave the system in an unknown state, such as unhandleable system errors (OOM) or errors caused by programmer oversight ("divide by zero", NPE).
- *Infrastructure Errors.* These are errors that are to be expected as part of the architecture but are not part of any business process and are not included in the domain, such as a network timeout or an authentication failure.

Domain errors are part of the domain, so should be incorporated into our domain modeling, discussed with domain experts, and documented in the type system, if possible.

Panics are best handled by abandoning the workflow and raising an exception that is then caught at the highest appropriate level. Here's an example:

```kotlin
// A workflow that panics if it gets bad input
val workflowPart2 = { input ->
    if (input == 0) {
        throw DivideByZeroException()
    }
    ...
}

// Top level function for the application
// which traps all exceptions from workflows
fun main() {
    // wrap all workflows in a try-catch block
    try {
        val result1 = workflowPart1()
        val result2 = workflowPart2(result1)
        println("the result is $result2")
    // top level exception handling
    } catch (e: OutOfMemoryException) {
        println("exited with OutOfMemoryException")
    } catch (e: DivideByZeroException) {
        println("exited with DivideByZeroException")
    } catch (e: Exception) {
        println("exited with ${e.message}")
    }
}
```

Infrastructure errors can be handled using either of the above approaches. Exact choice depends on the architecture. It's often useful to treat many infrastructure errors in the same way as domain errors, because it will force us as developers to think about what can go wrong.

### 10.2.1 Modeling Domain Errors in Types

```kotlin
sealed interface PlaceOrderError

@JvmInline
value class ValidationError(val value: String): PlaceOrderError

@JvmInline
value class ProductOutOfStock(val value: String): PlaceOrderError

data class RemoteServiceError(
    val service: ServiceInfo,
    val exception: Throwable
): PlaceOrderError
```

### 10.2.2 Error Handling Makes Your Code Ugly

## 10.3 Chaining Result-Generating Functions

### 10.3.1 Implementing the Adapter Blocks

### 10.3.2 Organizing the `Either` Functions

### 10.3.3 Composition and Type Checking

## 10.4 Using `bind/flatMap` and `map` in Our Pipeline

## 10.5 Adapting Other Kinds of Functions to the Two-Track Model

## 10.6 Making Life Easier with Computation Expressions

## 10.7 Monads and More

## 10.8 Adding the Async Effect
