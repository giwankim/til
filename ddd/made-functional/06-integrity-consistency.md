# 6. Integrity and Consistency in the Domain

The goal is to create a bounded context that always contains data we can trust as distinct from the untrusted outside world.

*Integrity* means that a piece of data follows the correct business rules.

*Consistency* means that different parts of the domain model agree about facts.

## Integrity for Simple Values

To ensure that the constraints are enforced, make the constructor private and have a separate function that creates valid values and rejects invalid values, returning an error instead. In FP communities, this is sometimes called the *smart constructor* approach.

```kotlin
@JvmInline
value class UnitQuantity private constructor(val value: Int) {
    companion object {
        operator fun invoke(value: Int): Either<ValidationError, UnitQuantity> {
            if (value < 1) {
                return Left(ValidationError("Unit quantity must be non-negative"))
            }
            return Right(UnitQuantity(value))
        }
    }
}
```
