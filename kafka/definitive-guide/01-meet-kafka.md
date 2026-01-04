# Meet Kafka

## Kafka

- Publish/Subscribe messaging system
- distributed commit log
- distributed streaming platform

### Messages and Batches

*Message* is the unit of data within Kafka. It can have an optional piece of metadata, which is
referred to as a *key*. Keys are used when messages are to be written to partitions in a more
controlled manner.

For efficiency, messages are written in batches. A *batch* is just a collection of messages, all of
which are being produced to the same topic and partition.

### Schemas

Messages are byte arrays to Kafka, it is recommended that additional structure, or schema, be
imposed on the message content.

### Topics and Partitions

Messages are categorized into *topics*.

Topics are broken down into a number of *partitions*.

Each partition can be hosted on a different server, which means that a single topic can be scaled
horizontally across multiple servers. Additionally, partitions can be replicated, such that
different servers will store a copy of the same partition.

The term *stream* is used when discussing data within systems like Kafka. A stream is considered
to be a single topic of data, regardless of the number of partitions.

### Producers and Consumers

There are two basic types of Kafka clients: producers and consumers. There are also advanced client
APIs--Kafka Connect API for data integration and Kafka Streams for stream processing.

*Producers* create new messages. A message will be produced to a specific topic.

*Consumers* read messages. The consumer subscribes to one or more topics and reads the messages in
the order in which they were produced to each partition. The consumer keeps track of which messages
it has already consumed by keeping track of the offset of messages.

Consumers work as part of a *consumer group*, which is one or more consumers that work together to
consume a topic. The group ensures that each partition is only consumed by one member.

### Brokers and Clusters

A single Kafka server is called a *broker*.

- Broker receives messages from producers, assigns offsets to them, and writes the messages to disk.
- Services consumers, responding to fetch requests for partitions and responding with the messages
  that have been published.

Brokers are designed to operate as part of a *cluster*. Within a cluster, one broker will also
function as the cluster *controller*. The controller is responsible for admin operations, including
assigning partitions to brokers and monitoring for broker failures.

A partition is owned by a single broker in the cluster called the *leader* of the partition. A
replicated partition is assigned to additional brokers called *followers* of the partition.

All producers must connect to the leader in order to publish messages, but consumers may fetch from
either the leader or one of the followers.

*Retention* is the durable storage of messages for some period of time.
