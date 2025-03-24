# 11. Payment System

According to Wikipedia,
> a payment system is any system used to settle financial transactions through the transfer of monetary value.

## Step 1 - Establish Design Scope

Some may think a payment system is a digital wallet like Apple Pay or Google Pay. Others may think it's backend system that handles payments such as PayPal or Stripe.

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

Simplified pay-in and pay-out flow:

![pay-in and pay-out](../../assets/system-design/interview2/pay-in-and-pay-out.png)

### Pay-in flow

![pay-in flow](../../assets/system-design/interview2/pay-in.png)

#### Payment service

Accepts payment events from users and coordinates the payment process. The first thing it usually does is a risk-check, assessing for compliance with regulations such as AML/CFT, and for evidence of criminal activity such as money laundering or financing of terrorism.

#### Payment executor

Executes a single payment order via a Payment Service Provider (PSP).

#### Payment Service Provider (PSP)

Moves money from account A to account B. In this simplified example, the PSP moves the money out of the buyer's credit card account.

#### Card schemes

Organizations that process credit card operations. Visa, MasterCard, Discovert, etc.

#### Ledger

Keeps a financial record of the payment transactions. For example, when a user pays the seller $1, we record it as debit $1 from a user and credit $1 to the seller.

The ledger system is very important in post-payment analysis, such as calculating the total revenue of the e-commerce website or forecasting future revenue.

#### Wallet

Keeps the account balance of the merchant. It may also record how much a given user has paid in total.

#### Pay-in flow sequence

Typical pay-in flow works like this:

1. When a user clicks the "place order" button, a payment event is generated and sent to the payment service.
2. The payment service stores the payment event in the database.
3. A single payment event may contain several payment orders. For example, you may select products from multiple sellers in a single checkout process. If the e-commerce website splits the checkout into multiple payment orders, the payment service calls the payment executor for each payment order.
4. Payment executor stores the payment order in the database.
5. Payment executor calls an external PSP to process the credit card payment.
6. After the payment executor has successfully processed the payment, payment service updates the wallet to record how much money a given seller has.
7. The wallet server stores the updated balance information in the database.
8. After the wallet service has successfully updated the seller's balance information, the payment service calls the ledger to update it.
9. The ledger service appends the new ledger information to the database.

### APIs for payment service

### Data model for payment

#### Double-entry ledger system
