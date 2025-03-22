# 11. Payment System

According to Wikipedia,
> a payment system is any system used to settle financial transactions through the transfer of monetary value.

## Step 1 - Establish Design Scope

### Functional requirements

- Pay-in flow: payment system receives money from customers on behalf of sellers.
- Pay-out flow: payment system sends money to sellers around the world.

### Non-functional requirements

- Reliability and fault tolerance. Failed payments need to be carefully handled.
- A reconciliation process between internal services (payment systems, accounting systems) and external services (payment service providers) is required. The process asynchronously verifies that the payment information across these system is consistent.

### Back-of-the-envelope estimation

- 1 million transactions per day.
- 1,000,000 transactions / $10^5$ seconds = 10 TPS.

10 TPS is not a big number for a typical database, which means the focus of this system of this system design is on how to correctly handle payment transactions, rather than aiming for high throughput.

## Step 2 - High-Level Design
