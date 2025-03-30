# 12. Digital Wallet

Payment platforms usually provide a digital wallet service to clients, so they can store money in the wallet and spend it later.

![digital wallet](../../assets/system-design/interview2/digital-wallet.png)

Design the backend of a digital wallet application that supports the cross-wallet balance transfer operation.

![cross-wallet balance transfer](../../assets/system-design/interview2/balance-transfer.png)

## Step 1 - Establish Design Scope

### Requirements

- Support balance transfer operation between two digital wallets.
- Support 1,000,000 TPS.
- Availability is at least 99.99%.
- Support transactions.
- Support reproducibility: can reconstruct historical balance by replaying the data from the very beginning.

### Back-of-the-envelope estimation

Assume a database node can support 1,000 TPS.

Each transfer command requires two operations: deducting money from one account and depositing money to the other account.

| Per-node TPS | Node Number |
| ------------ | ----------- |
| 100 | 20,000 |
| 1,000 | 2,0000 |
| 10,000 | 200 |
