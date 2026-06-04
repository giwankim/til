# What do you mean by "Event-Driven"?

> Source: [martinfowler.com/articles/201701-event-driven.html](https://martinfowler.com/articles/201701-event-driven.html)
> Authors: Martin Fowler (with the ThoughtWorks event-architecture group)

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

It implies a low level of coupling between the sender from the receiver.

### "Where did this happen?" traceability problem

It can become problematic, however, if there really is a logical flow that runs over various event notifications. The problem is that it can be hard to see such a flow as it's not explicit in any program text. Often the only way to figure out this flow is from monitoring a live system.

Simple example of this trap is when an event is used as a passive-aggressive command.

## 2. Event-Carried State Transfer

<!-- What it is: events carry enough data that receivers don't call back.
     - Trade-off: reduced coupling / availability vs. data duplication & eventual consistency -->

## 3. Event-Sourcing

<!-- What it is: store the sequence of events as the system of record; current
     state is a derivation.
     - Rebuilding state, audit log, temporal queries
     - Pitfalls: external systems, versioning event schemas, snapshots -->

## 4. CQRS

<!-- What it is: separate the model for reading from the model for writing.
     - Relationship to event-sourcing (often paired, not required)
     - When the added complexity is/ isn't justified -->

## Key Distinctions & When to Use Each

<!-- Pull together the contrasts: which problems each pattern actually solves,
     and Fowler's caution about adopting them without clear benefit. -->

## My Takeaways

<!-- Your own notes / how this applies to your work. -->
