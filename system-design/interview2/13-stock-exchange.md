# 13. Stock Exchange

Design an electronic stock exchange systems.

## Step 1 - Establish Design Scope

### Functional requirements

- Trade stocks during normal trading hours.
- Place a new order and canceling an order.
- Support limit orders.
- Risk checks. For example, a user can only trade a maximum of 1 million shares of Apple stock in one day.
- Wallet management. Make sure users have sufficient funds when they place orders.

### Non-functional requirements

- Availability. At least 99.99%.
- Fault tolerance. Fault tolerance and fast recovery mechanism are needed to limit the impact of production incident.
- Latency. Round-trip latency should be at the millisecond level, with a particular focus on the 99th percentile latency.
- Security. Should have an account management system. For legal compliance, the exchange performs a KYC check to verify user's identity before a new account is opened. For public resources, such as web pages containing market data, we should prevent DDoS attacks.

### Back-of-the-envelope estimation

- 100 symbols
- 1 billion orders per day
- NYSE open Monday through Friday from 9:30 AM to 4:00 PM EST. That's 6.5 hours in total.
- QPS: 1 billion / 6.5 / 3600 = ~43,000
- Peak QPS: 5 x QPS = 215,000. The trading volume is significantly higher when the market first opens in the morning and before it closes in the afternoon.

## Step 2 - High-Level Design

### Business Knowledge 101

#### Broker

#### Institutional client

#### Limit order

#### Market order

#### Market data levels

US stock market has three tiers of prices quotes: L1, L2, and L3.

### High-level design

![high-level design](../../assets/system-design/interview2/stock-exchange-high-level-design.png)
