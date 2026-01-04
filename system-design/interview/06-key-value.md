# 6. Design a Key-Value Store

A key-value store, also referred to as a key-value database, is a non-relational database where each _unique_ identifier is stored as a key with its associated value. This data pairing is known as a "key-value" pair.

In this chapter, you are asked to design a key-value store that supports the following operations:

- `put(key, value) // insert "value" associated with "key"`
- `get(key) // get "value" associated with "key"`

## Understand the problem and establish design scope

Each design achieves a specific balance regarding the tradeoffs of read, write, and memory usage. Another tradeoff has to be made between _consistency_ and _availability_.

- The size of key-value pair is small: less than 10 KB.
- Ability to store big data.
- High availability: system responds quickly, even during failures.
- High scalability: system can be scaled to support large data set.
- Automatic scaling: addition/deletion of servers should be automatic based on traffic.
- Tunable consistency.
- Low latency.

## Single server key-value store

An intuitive approach is to store key-value pairs in a hash table, which keeps everything in memory. Even though memory access is fast, fitting everything in memory may be impossible due to space constraint. Two optimizations can be done to fit more data in a single server:

- compression
- store only frequently

Even with these optimizations, a single server can reach its capacity very quickly.

A distributed key-value store is required to support big data.

## Distributed key-value store

### CAP theorem

CAP theorem states that it is impossible for a distributed system to simultaneously provide more than two of these guarantees: _consistency, availability, and partition tolerance._

Distribute systems are classified based on the two CAP characteristics they support:

- CP systems
- AP systems
- CA systems
