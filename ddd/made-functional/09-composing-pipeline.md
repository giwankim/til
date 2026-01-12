# 9. Implementation: Composing a Pipeline

The workflow can be thought of as a series of document transformations--a pipeline--with each step in the pipeline designed as a section of a "pipe."

- Start with an `UnvalidatedOrder` and convert it into a `ValidatedOrder`, returning an error if the validation fails.
- Take the output of the validation step (a `ValidatedOrder`) and turn it into a `PricedOrder` by adding some extra information.
- Take the output of the pricing step, create an acknowledgement letter from it, and send it.
- Create a set of events representing what happened and return them.

Functions can't be composed for two reasons:

- Some functions have extra parameters that aren't part of the data pipeline--we called these "dependencies."
- The second reason is explicitly indicated "effects," which means that functions with effects in their output cannot be directly connected to other functions.

## 9.1 Working with Simple Types

For each simple type, we'll need at least two functions:

- A `create` function that constructs the type from a primitive.
- A `value` function that extracts the inner primitive value.

```kotlin
@JvmInline
value class OrderId private constructor(val value: String) {
    companion object {
        operator fun invoke(str: String): OrderId {
            require(str.isNotEmpty()) { "OrderId must not be null or empty" }
            require(str.length <= 50) { "OrderId must not be more than 50 chars" }
            return OrderId(str)
        }
    }
}
```

## 9.2 Using Function Types to Guide the Implementation

We defined special function types to represent each step of the workflow. How can we ensure that our code conforms to them?

The simplest approach is just to define a function in the normal way and trust that when we use it later, we'll get a type-checking error. For example, we could define the `validateOrder` function as below, with no reference to the `ValidateOrder` type that we designed earlier:

```kotlin
fun UnvalidatedOrder.validateOrder(       // input
    checkProduct: CheckProductCodeExists, // dependency
    checkAddress: CheckAddressExists      // dependency
): ValidatedOrder {
    ...
}
```

But if we want to make it clear that we are implementing a specific function type, we can write the function as a value annotated with the function type, with the body of the function written as a lambda.

```kotlin
// define a function signature
typealias ValidateOrder = UnvalidatedOrder.(CheckProductCodeExists, CheckAddressExists) -> ValidatedOrder

val validateOrder: ValidateOrder = { checkProduct, checkAddress ->
    ...
}
```

All the parameters and the return value have types determined by the function type, so if you make a mistake, you get an error right away, rather than later when you are trying to assemble the function.

## 9.3 Implementing the Validation Step

The validation step will take the unvalidated order with all its primitive fields, and transform it into a fully validated domain object.

We modeled the function types for this step like this:

```kotlin
typealias CheckAddressExists = suspend (UnvalidatedAddress) ->
    Either<AddressValidationError, CheckedAddress>

typealias ValidateOrder = suspend UnvalidatedOrder.(
    CheckAddressExists,    // async, error dependency
    CheckProductCodeExists // dependency
) -> Either<List<ValidationError>, ValidatedOrder>
```

We'll eliminate effects for this chapter.

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

### 9.3.1 Valid, Checked Address

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

### 9.3.2 Order Lines

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

### 9.3.3 Function Adapters

Rather than changing the spec, let's create an "adapter" function that takes the original function as input and emits a new function with the right "shape" to be used in the pipeline.

```kotlin
fun String.convertToPassthru(checkProductCodeExists: (String) -> Boolean): String {
    require(checkProductCodeExists(this)) { "Invalid Product Code" }
    return this
}
```

In fact, this implementation is completely generic. If you look at the function signature, you'll see there's no mention of the `ProductCode` type anywhere:

```kotlin
fun <T> T.predicateToPassthru(f: (T) -> Boolean): T {
    require(f(this)) { "Invalid Product Code" }
    return this
}
```

And now the hard-coded error message sticks out, so let's parameterize that too:

```kotlin
fun <T> T.predicateToPassthru(errorMsg: String, f: (T) -> Boolean): T {
    require(f(this)) { errorMsg }
    return this
}
```

We can interpret this as, "You give me an error message and a function of type `T -> Boolean`, and I'll give you back a function of type `T -> T`."
This `predicateToPassthru` function is thus a "function transformer."

This technique is extremely common in FP. Even the humble `List.map` function can be thought of as a function transformer--it transforms a "normal" function `T -> U` into a function that works on lists (`List<T> -> List<U>`).

OK, now let's use this generic function to create a new version of `toProductCode` that we can use:

```kotlin
fun String.toProductCode(checkProductCodeExists: CheckProductCodeExists): ProductCode {
    return this.let { 
            ProductCode(it)
                .predicateToPassthru("Invalid: $it", checkProductCodeExists) 
        }
}
```

Since Kotlin does not have a built-in pipe operator like some FP languages, `predicateToPassthru` may be excessive abstraction and the same could be implemented as follows:

```kotlin
fun String.toProductCode(checkProductCodeExists: CheckProductCodeExists): ProductCode {
    val productCode = ProductCode(this)
    require(checkProductCodeExists(productCode)) { "Invalid: $productCode" }
    return productCode
}
```

Notice that the low-level validation logic, such as "a product must start with a W or a G," is not explicitly implemented in our validation functions but is built into the constructors of the constrained simple types, such as `OrderId` and `ProductCode`.

## 9.4 Rest of the Steps

Original design of the pricing step function with effects:

```kotlin
typealias PriceOrder = ValidatedOrder.(GetProductPrice) -> Either<PlaceOrderError, PricedOrder>
```

Eliminate effects for now:

```kotlin
typealias PriceOrder = ValidatedOrder.(GetProductPrice) -> PricedOrder
```

And here's the outline of the implementation. It simply transforms each order line to a `PricedOrderLine` and builds a new `PricedOrder` with them:

```kotlin
val priceOrder: PriceOrder = { getProductPrice ->
    val pricedLines = this.lines.map { it.toPricedOrderLine(getProductPrice) }
    val linePrices = pricedLines.map { it.linePrice }
    val amountToBill = BillingAmount.sumPrices(linePrices)
    return PricedOrder(
        this.orderId,
        this.customerInfo,
        this.shippingAddress,
        this.billingAddress,
        pricedLines,
        amountToBill,
    )
}
```

By the way, if you have many steps in the pipeline that you don't want to implement yet, you can just throw a `NotImplementedError` with `TODO`:

```kotlin
fun priceOrder(): PriceOrder = TODO()
```

Going back to the implementation of `priceOrder`, we've introduced two new helper functions: `toPricedOrderLine` and `BillingAmount.sumPrices`.

Why have we defined a `BillingAmount` type in the first place? It is distinct from a `Price`, and the validation rules might be different.

```kotlin
@JvmInline
value class BillingAmount private constructor(val value: BigDecimal) {
    companion object {
        operator fun invoke(v: BigDecimal): BillingAmount { ... }
        fun sumPrices(prices: List<Price>): BillingAmount {
            return invoke(prices.sumOf { it.value })
        }
    }
}
```

The `toPricedOrderLine` function is similar to what we've seen before. It's a helper function that converts a single line only:

```kotlin
val toPricedOrderLine: ValidatedOrderLine.(GetProductPrice) -> PricedOrderLine = { getProductPrice ->
    val price = getProductPrice(this.productCode)
    return PricedOrderLine(
        this.orderLineId,
        this.productCode,
        this.quantity,
        price.multiply(this.quantity),
    )
}
```

And within this function, we've introduced another helper function `Price.multiply`, to multiply a `Price` by a quantity.

```kotlin
@JvmInline
value class Price private constructor(val value: BigDecimal) {
    ...
    fun multiply(quantity: Double): Price {
        return invoke(this.value * quantity.toBigDecimal())
    }
}
```

### 9.4.1 Acknowledgement Step

### 9.4.2 Events

### 9.4.3 Creating the Events

## 9.5 Composing the Pipeline Steps Together

## 9.6 Injecting Dependencies

## 9.7 Testing Dependencies

## 9.8 Assembled Pipeline
