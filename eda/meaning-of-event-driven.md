# What do you mean by "Event-Driven"?

> Source: [martinfowler.com/articles/201701-event-driven.html](https://martinfowler.com/articles/201701-event-driven.html)
> Authors: Martin Fowler (with the ThoughtWorks event-architecture group)
> Supplementary (long-form video): [The Many Meanings of Event-Driven Architecture — Martin Fowler (GOTO 2017)](https://www.youtube.com/watch?v=STKCRSUsyP0)

"Event-Driven" is an overloaded term. People use it to mean at least four distinct patterns that get conflated.

- Event Notification
- Event-Carried State Transfer
- Event-Sourcing
- CQRS

## The Problem: "Event-Driven" Conflates Several Patterns

## 1. Event Notification

This happens when a system sends event messages to notify other systems of a change in its domain. A key element of event notification is that the source system doesn't really care much about the response.

An event need not carry much data on it, often just some id information and a link back to the sender that can be queried for more information.

### Decouples sender from receiver

It implies a low level of coupling between the sender and the receiver.

### "Where did this happen?" traceability problem

It can become problematic, however, if there really is a logical flow that runs over various event notifications. The problem is that it can be hard to see such a flow as it's not explicit in any program text. Often the only way to figure out this flow is from monitoring a live system.

A simple example of this trap is when an event is used as a passive-aggressive command.

## 2. Event-Carried State Transfer

This pattern shows up when you want to update clients of a system in such a way that they don't need to contact the source system in order to do further work.

### Trade-offs

An obvious downside of this pattern is that there's lots of data schlepped around and lots of copies. What we gain is greater resilience. We reduce latency and improve availability, at the cost of increased complexity and eventual consistency.

## 3. Event-Sourcing

<!-- What it is: store the sequence of events as the system of record; current
     state is a derivation.
     - Rebuilding state, audit log, temporal queries
     - Pitfalls: external systems, versioning event schemas, snapshots -->

The core idea of event sourcing is that whenever we make a change to the state of a system, we record that state change as an event, and we can rebuild the system state by reprocessing the events at any time in the future. The event store becomes the principal source of truth, and the system state is purely derived from it.

### Misconceptions

There's no need for event processing to be asynchronous.

Everyone using an event-sourced system should understand and access the event log to determine useful data. Much of the processing in an event-sourced system can be based on a useful working copy. Usually there should be a clear separation between domain processing and deriving a working copy from the event log.

### Snapshots

When working with an event log, it is often useful to build snapshots of the working copy. There is a duality here: we can look at the event log as either a list of changes or a list of states.

### Benefits

The event log provides a strong audit capability (account transactions are an event source for account balances). We can recreate historic states by replaying the event log up to a point. We can explore alternative histories by injecting hypothetical events when replaying. Event sourcing makes it plausible to have non-durable working copies, such as a Memory Image.

### Pitfalls

Replaying events becomes problematic when results depend on interactions with outside systems.

Have to figure out how to deal with changes in the schema of events over time.

## 4. CQRS

Command Query Responsibility Segregation (CQRS) is the notion of having separate data structures for reading and writing information. You can use CQRS without any events present in your design, but commonly people do combine CQRS with the earlier patterns here.

### Justification

In complex domains, a single model to handle both reads and writes gets too complicated, and we can simplify by separating the models. This is particularly appealing when you have different access patterns, such as lots of reads and very few writes.

### Trade-offs

The gain from CQRS has to be balanced against the additional complexity of having separate models.

## Key Distinctions & When to Use Each

<!-- Pull together the contrasts: which problems each pattern actually solves,
     and Fowler's caution about adopting them without clear benefit. -->

## My Takeaways

<!-- Your own notes / how this applies to your work. -->
