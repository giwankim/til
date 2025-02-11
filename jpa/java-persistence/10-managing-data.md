# 10. Managing data

## 10.1 The persistence lifecycle

Any application with a persistent state must interact with the persistence service (Jakarta Persistence interface) to store and load data.

When interacting with the persistence mechanism this way, the application must concern itself with the state and lifecycle of an entity instance with respect to persistence.

### 10.1.1 Entity instance states

JPA defines four states and their transitions.

![entity instance states](../../assets/jpa/java-persistence/entity-instance-states.png)

#### TRANSIENT STATE

Instances created with the `new` Java operator are _transient_.

For an entity instance to transition from transient to _persistence_ state requires either a call to the `EntityManager#persist()` method or cascading of state from an already persistent instance.

#### PERSISTENT STATE

A _persistent_ entity instance has a representation in the database. It's stored in the database-or it will be stored when the unit of work completes.

Persistent instances are always associated with a persistence context.

#### REMOVED STATE

We can delete a persistent entity instance from the database in several ways. We can remove it with `EntityManager#remove()`. It may also become available for deletion if we remove a reference to it from a mapped collection with _orphan removal_ enabled.

An entity instance is then in the _removed_ state: the provider will delete it at the end of a unit of work.

#### DETACHED STATE

Consider loading an instance by calling `EntityManager#find()`, then ending our unit of work and close the persistence context.
The application still has a _handle_-a reference to the instance we loaded. It's now in a detached state, and the data is becoming stale.

### 10.1.2 The persistent context

## 10.2 The `EntityManager` interface
