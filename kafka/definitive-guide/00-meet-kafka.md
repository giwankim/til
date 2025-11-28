# Meet Kafka

## Kafka

- Publish/Subscribe messaging system
- distributed commit log
- distributed streaming platform

### Messages and Batches

*Message* is the unit of data within Kafka. It can have optional piece of metadata, which is
referred to as a *key*. Keys are used when messages are to be written to partitions in a more
controlled manner.

For efficiency, messages are written in batches. A *batch* is just collection of messages, all of
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

The term *stream* is used when discussing data within a systems like Kafka. A stream is considered
to be a single topic of data, regardless of the number of partitions.
