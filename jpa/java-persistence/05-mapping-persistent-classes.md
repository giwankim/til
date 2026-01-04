# 5. Mapping persistent classes

## 5.1 Understanding entities and value types

### 5.1.1 Fine-grained domain models

A major objective of Hibernate and Spring Data JPA, as persistence providers, is providing support for fine-grained and rich domain models. It's one reason we work with POJOs. In crude terms, _fine grained_ means having more classes than tables.

### 5.1.2 Defining application concepts

- _Entity type_: You can retrieve an instance of an _entity type_ using its persistent identity. A reference to an entity instance (a pointer in the JVM) is persisted as a reference in the database (a foreign key-constrained value). An entity instance has its own lifecycle.
- _Value type_: An instance of a _value type_ has no persistent identifier property; it belongs to an entity instance, and its lifespan is bound to the owning entity instance.

## 5.2 Mapping entities with identity
