# Learn Apache Kafka for Beginners v3

## 1. Kafka Introduction

Move data from source system to target system. Can get complicated:

- Multiple integrations as sources and targets increase.
- Each integration comes with difficulites around
  - Protocol: how the data in transported (TCP, HTTP, REST, FTP, JDBC, ...)
  - Data format: how the data is parsed (Binary, CSV, JSON, Avro, Protobuf, ...)
  - Data schema & evolution: how the data is shaped may change over time
- Each source system will have an *increased load* from the connections.

Why Kafka? It allows decoupling of data streams and systems.

- Distributed, resilient architecture, fault tolerant
- Horizontal scalability
  - Can scale to 100s of brokers
  - Can scale to millions of messages per second
- High performance (latency of less than 10ms) - real time

Use cases:

- Messaging system
- Activity tracking
- Gather metrics from many different locations
- Application log gathering
- Stream processing (Kafka Streams API)
- De-coupling of system dependencies
- Integration with Spark, Flink, Storm, Hadoop, etc...
- Microservices pub/sub

## 3. Kafka Theory

### Topics, Partitions and Offsets

Topics: a particular stream of data

- Like a table in a database (without all the constraints)
- Can have as many topics as you want
- Identified by its name
- Any kind of message format
- Sequence of messages is called a data stream
- Cannot query topics, instead use Kafka Producers to send data. Kafka Consumers read the data.

Topics are split in *partitions*.

- Messages within each partition are ordered
- Each message within a partition gets an incremental id, called *offset*

Important notes

- Kafka topics are immutable: once data is written to a partition, it cannot be changed.
- Data is kept only for a limited time (default is one week - configurable)
- Offset only has meaning for a specific partition
- Order is guaranteed only within *a* partition
- Data is assigned randomly to a partition unless a key is provided
- Can have as many partitions per topic as you want

### Producers and Message Keys

Producers

- Producers write data to topics (which are made of partitions)
- Producers know to which parition to write to (and which Kafka broker has it)
- In case of broker failures, producers will automatically recover

Write load is balanced to many brokers thanks to the number of partitions.

Message keys

- Producer can choose to send a *key* with the message (string, number, binary, etc...)
- If key is null, data is sent round robin (Kafka 2.4+ uses sticky partitioning - batches message to same partition until batch is full)
- if key != null, then all messages for that key will always go to the same partition (hashing)
- Typically, a key is sent if you need message ordering for a specific field

Kafka message created by the producer consists of

- Key - binary (can be null)
- Value - binary (can be null)
- Compression type (none, gzip, snappy, lz4, zstd)
- Headers (optional)
- Partition + Offset
- Timestamp (system or user set)

Kafka Message Serializer

- Kafka only accepts bytes as an input from producers and sends bytes out as output to consumers
- Message serialization means transforming objects / data into bytes
- Used on the value and the key
- Common serializers:
  - String (incl. JSON)
  - Int, Float
  - Avro
  - Protobuf

Kafka partitioner is a code logic that takes a record and determines to which partition to send it into.
In the default Kafka partitioner, the keys are hashed using the Murmur2 algorithm

```pseudocode
targetPartition = abs(Utils.murmur2(keyBytes)) % numPartitions
```

### Consumers and Deserialization

Consumers

- Consumers read data from a topic - pull model
- Consumers automatically know which broker to read from
- In case of broker failures, consumers know how to recover
- Data is read in order from low to high offset *within each partition*

Consumer Deserializer

- Deserialization indicates how to transform bytes into objects/data
- They are used on the value and the key of the message
- Common deserializers
  - String (incl. JSON)
  - Int, Float
  - Avro
  - Protobuf
- The serialization/deserialization type must not change during topic lifecycle (create a new topic instead)

### Consumer Groups and Consumer Offsets

### Brokers and Topics

### Topic Replication

### Producer Acknowledgments and Topic Durability

### Zookeeper

### Kafka KRaft - Removing Zookeeper

### Theory Roundup
