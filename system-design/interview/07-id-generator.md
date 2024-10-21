# 7. Design a Unique ID Generator in Distributed Systems

First thought might be to use a primary key with the *auto_increment* attribute in a traditional database. However, _auto\_increment_ does not work inn a distributed environment because generating unique IDs across multiple databases with minimal delay is challenging.

# Step 1 - Understand the problem and establish design scope

__Candidate:__ What are the characteristics of unique IDs?

__Interviewer:__ IDs must be unique and sortable.

__Candidate:__ For each new record, does ID increment by 1?

__Interviewer:__ The ID increments by time but not necessarily only increment by 1.

__Candidate:__ Do IDs only contain numerical values?

__Interviewer:__ Yes.

__Candidate:__ What is the ID length requirement?

__Interviewer:__ IDs should fit into 64-bit.

__Candidate:__ What is the scale of the system?

__Interviewer:__ The system should be able to generate 10,000 IDs per second.

- IDs must be unique.
- IDs are numerical values only.
- IDs fit into 64-bit.
- IDs are ordered by date.
- Generate over 10,000 unique IDs per second.

# Step 2 - Propose high-level design and get buy-in

## Multi-master replication

The first approach is multi-master replication.

MySQL multi-master replication is a database configuration where two or more MySQL servers (masters) simultaneously handle read and write operations, and replicate data changes to each other.

This approach uses the databases' *auto_increment* feature. Instead of increasing next ID by 1, we increase it by k, where k is the number of database servers in use.

### Cons

- Hard to scale with multiple data centers.
- IDs do not go up with time across multiple servers.
- It does not scale well when a server is added or removed.

## UUID

UUID is 128-bit number used to identify information in computer systems. UUID has a very low probability of getting collision.

Here is an example of UUID: `dffef5c0-d6d2-4080-a78b-fa9f1d924a1a`.

UUIDs can be generated independently without coordination between servers.

### Pros

- No coordination between servers is needed so there will not be any synchronization issues.
- Easy to scale.

### Cons

- IDs are 128-bits long, but our requirement is 64-bits.
- IDs do not go up with time.
- IDs could be non-numeric.

## Ticket Server

Flickr developed ticket servers to generate distributed primary keys.

 The idea is to use a centralized *auto_increment* feature in a single database server (Ticket Server).

### Pros

- Numeric IDs.
- Easy to implement, and it works for small to medium-scale applications.

### Cons

- SPOF. To avoid a single point of failure, we can set up multiple ticket servers. However, this introduces new challenges such as synchronization.

## Snowflake

None of the above methods meet our specification. Twitter's unique ID generation system called "snowflake" can satisfy our requirements.

```mermaid
---
title: Snowflake
---
packet-beta
0: "Sign bit"
1-42: "timestamp"
```

### Pros

### Cons

# Step 3 - Design deep dive

# Step 4 - Wrap up
