# 6. Design a Key-Value Store

A key-value store, also referred to as a key-value database, is a non-relational database where each _unique_ identifier is stored as a key with its associated value. This data pairing is known as a "key-value" pair.

In this chapter, you are asked to design a key-value store that supports the following operations:

- `put(key, value) // insert "value" associated with "key"`
- `get(key) // get "value" associated with "key"`

# Understand the problem and establish design scope

Each design achieves a specific balance regarding the tradeoffs of read, write, and memory usage.

Another tradeoff between _consistency_ and _availability_.

- The size of key-value pair is small: less than 10 KB.
- Ability to store big data.
- High availability
- High scalability
- Automatic scaling
- Tunable consistency
- Low latency

