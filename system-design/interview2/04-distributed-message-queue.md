# 4. Distributed Message Queue

What benefits do message queues bring?

- Decoupling. Eliminate tight coupling between components so they can be updated independently.
- Improved scalability. Scale producers and consumers independently based on traffic load.
- Increased availability. If one part of the system goes offline, the other components can continue interact with the queue.
- Better performance. Message queues make asynchronous communication easy.

> [!TIP]
> Apache Kafka and Pulsar are not message queues as they are event streaming platforms.

## Step 1 - Understand the Problem and Establish Design Scope

### Functional requirements

- Producers send messages to a message queue.
- Consumers consume messages from a message queue.
- Messages can be consumed repeatedly or only once.
- Historical data can be truncated.
- Message size is in the kilobyte range.
- Ability to deliver messages to consumers in the order they were added to the queue.
- Data delivery semantics (at-least-once, at-most-once, or exactly-once) can be configured by users.

### Non-functional requirements

- Configurable high throughput or low latency.
- Scalable.
- Persistent and durable. Data should be persisted on disk and replicated across multiple nodes.

> [!NOTE]
> Message queues like RabbitMQ do not have as strong a retention requirement as event streaming platforms. Queues do not typically maintain message ordering. These differences greatly simplify the design which we will discuss where appropriate.
