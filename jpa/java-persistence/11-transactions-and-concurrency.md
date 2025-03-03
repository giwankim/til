# 11. Transactions and concurrency

A _unit of work_ is an atomic group of operations, and transactions allow us to set unit of work boundaries and help us isolate one unit of work from another.

## 11.1 Transaction essentials

_Atomicity_

Correctness of a transaction is the responsibility of the application, whereas consistency is the responsibility of the database.
