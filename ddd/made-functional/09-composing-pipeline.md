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

## Using Function Types to Guide the Implementation

We defined special function types to represent each step of the workflow. How can we ensure that our code conforms to them?

The simplest approach is just to define a function in the normal way and trust that when we use it later, we'll get a type-checking error. For example, we could define the `validateOrder` function as below, with no reference to the `ValidateOrder` type that we designed earlier:

```kotlin
fun UnvalidatedOrder.validateOrder(           // input
    checkProduct: CheckProductCodeExists, // dependency
    checkAddress: CheckAddressExists      // dependency
): ValidatedOrder {
    ...
}
```

But if we want to make it clear that we are implementing a specific function type, we can write the function as a value annotated with the function type, and with the body of the function written as a lambda.

```kotlin
// define a function signature
typealias ValidateOrder = UnvalidatedOrder.(CheckProductCodeExists, CheckAddressExists) -> ValidatedOrder

val validateOrder: ValidateOrder = { checkProduct, checkAddress ->
    ...
}
```

## Implementing the Validation Step

The validation step will take the unvalidated order and transform it into a fully validated domain object.

We modeled the function types for this step like this:

```kotlin
typealias CheckAddressExists = suspend (UnvalidatedAddress) ->
    Either<AddressValidationError, CheckedAddress>

typealias ValidateOrder = suspend UnvalidatedOrder.(
    CheckAddressExists,
    CheckProductCodeExists
) -> ValidatedOrder
```

The steps to create a `ValidatedOrder` from an `UnvalidatedOrder` will be as follows:

- Create an `OrderId` domain type from the corresponding `OrderId` string in the unvalidated order.
- Create a `CustomerInfo` domain type from the corresponding `UnvalidatedCustomerInfo` field in the unvalidated order.
- Create an `Address` domain type from the corresponding `ShippingAddress` field in the unvalidated order, which is an `UnvalidatedAddress`.
- Do the same for `BillingAddress` and all the other properties.
- Once we have all the components of the `ValidatedOrder` available, we can then create the record in the usual way.

```kotlin
val validateOrder: ValidateOrder = { checkProductCodeExists, checkAddressExists ->
    val orderId = this.orderId.toOrderId()
    val customerInfo = this.customerInfo.toCustomerInfo()
    val shippingAddress = this.shippingAddress.toAddress()

    // and so on, for each property of the unvalidatedOrder

    // when all the fields are ready, use them to
    // create and return a new "ValidatedOrder" record
    ValidatedOrder(
        orderId = orderId,
        customerInfo = this.customerInfo,
        shippingAddress = this.shippingAddress,
        billingAddress = ...,
        lines = ...
    )
}
```

We are using some helper functions, such as `toCustomerInfo` and `toAddress`. These functions construct a domain type from an unvalidated type.

Once we have all these helper functions in place, the logic to convert an unvalidated order to a domain type is straightforward: for each field of the domain type (`ValidatedOrder`), we find the corresponding field of the non-domain type (`UnvalidatedOrder`) and use one of the helper functions to convert it.

We can use the same approach when converting the subcomponents of an order as well. Here is the implementation of `toCustomerInfo`, which builds a `CustomerInfo` from an `UnvalidatedCustomerInfo`:

```kotlin
fun UnvalidatedCustomerInfo.toCustomerInfo(): CustomerInfo {
    // create the various CustomerInfo properties
    // and throw exceptions if invalid
    val firstName = String50(this.firstName)
    val lastName = String50(this.lastName)
    val emailAddress = EmailAddress(this.emailAddress)

    // create a PersonalName
    val name = PersonalName(firstName, lastName)

    // create a CustomerInfo
    // and return it
    return CustomerInfo(
        name = name,
        emailAddress = emailAddress,
    )
}
```

### Valid, Checked Address

The `toAddress` function is a bit more complex since it also has to check that the address exists (using the `CheckAddressExists` service).

```kotlin
fun UnvalidatedAddress.toAddress(checkAddressExists: CheckAddressExists): Address {
    // call the remote service
    val checkedAddress = checkAddressExists(this)

    val addressLine1 = String50(checkedAddress.addressLine1)
    val addressLine2 = checkedAddress.addressLine2?.let { String50(it) }
    val addressLine3 = checkedAddress.addressLine3?.let { String50(it) }
    val addressLine4 = checkedAddress.addressLine4?.let { String50(it) }
    val city = String50(checkedAddress.city)
    val zipCode = ZipCode(checkedAddress.zipCode)

    // create the address
    // and return the address
    return Address(
        addressLine1 = addressLine1,
        addressLine2 = addressLine2,
        addressLine3 = addressLine3,
        addressLine4 = addressLine4,
        city = city,
        zipCode = zipCode,
    )
}
```

The `toAddress` function needs to call `checkAddressExists`:

```kotlin
val validateOrder: ValidateOrder = { checkProductCodeExists, checkAddressExists ->
    val orderId = ...
    val customerInfo = ...
    val shippingAddress = this.shippingAddress.toAddress(checkAddressExists)

    ...
}
```

### Order Lines

Creating the list of order lines is even more complex. First, we need to transform a single `UnvalidatedOrderLine` to a `ValidatedOrderLine`.

```kotlin
fun UnvalidatedOrderLine.toValidatedOrderLine(
    checkProductCodeExists: CheckProductCodeExists
): ValidatedOrderLine {
    val orderLineId = OrderLineId(this.orderLineId)
    val productCode = this.productCode.toProductCode(checkProductCodeExists) // helper function
    val quantity = this.quantity.toOrderQuantity(this.productCode) // helper function
    return ValidatedOrderLine(
        orderLineId,
        productCode,
        quantity,
    )
}
```

There are two helper functions, `toProductCode` and `toOrderQuantity`, which we will discuss shortly.

We can now transform the whole list at once using `map`:

```kotlin
val validateOrder: ValidateOrder = { checkProductCodeExists, checkAddressExists ->
    val orderId = ...
    val customerInfo = ...
    val shippingAddress = ...
    val orderLines = this.lines.map { it.toValidatedOrderLine(checkProductCodeExists) }
    ...
}
```

Let's look at the helper function `toOrderQuantity` next. This is a good example of validation at the boundary of a Bounded Context: the input is a raw unvalidated decimal from `UnvalidatedOrderLine`, but the output (`OrderQuantity`) is a choice type with different validations for each case.

```kotlin
fun Double.toOrderQuantity(productCode: ProductCode): OrderQuantity {
    return when (productCode) {
        is Widget -> OrderQuantity.Unit(this.toInt())
        is Gizmo -> OrderQuantity.Kilogram(this)
    }
}
```

The other helper function, `toProductCode`, might seem straightforward at first glance. The code should look something like this:

```kotlin
fun String.toProductCode(checkProductCodeExists: CheckProductCodeExists): ProductCode {
    return ProductCode(this).checkProductCodeExists()
    // returns a boolean :(
}
```

But now we have a problem. We want the `toProductCode` function to return a `ProductCode`, but it returns a `Boolean`. We need to find a way to make `checkProductCodeExists` return a `ProductCode` instead.

### Function Adapters
