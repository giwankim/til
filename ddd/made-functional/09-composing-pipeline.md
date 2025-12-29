# 9. Implementation: Composing a Pipeline

The workflow can be thought of as a series of document transformations--a pipeline--with each step in the pipeline designed as a section of a "pipe."

- Start with an `UnvalidatedOrder` and convert it into a `ValidatedOrder`, returning an error if the validation fails.
- Take the output of the validation step (a `ValidatedOrder`) and turn it into a `PricedOrder` by adding some extra information.
- Take the output of the pricing step, create an acknowledgment letter from it, and send it.
- Create a set of events representing what happened and return them.

## Working with Simple Types

For each simple type, we'll need at least two functions:

- A `create` function that constructs the type from a primitive.
- A `value` function that extracts the inner primitive value.

```kotlin
@JvmInline
value class OrderId(val value: String) {
    companion object {
        operator fun invoke(str: String): OrderId {
            require(str.isNotEmpty()) { "OrderId must not be null or empty" }
            require(str.length <= 50) { "OrderId must not be more than 50 chars" }
            return OrderId(str)
        }
    }
}
```
