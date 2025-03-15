# 8. Distributed Email Service

## Step 1 - Establish Design Scope

### Functional requirements

- Send and receive emails.
- Fetch all emails.
- Filter emails by read and unread status.
- Search emails by subject, sender, and body.
- Anti-spam and anti-virus.

### Non-functional requirements

- Reliability: Should not lose email data.
- Availability: Email and user data should be automatically replicated across multiple nodes to ensure availability. The system should continue to function despite partial system failures.
- Scalability: The performance of the system should not degrade with more users or emails.
- Flexibility and extensibility: Traditional email protocols such as POP and IMAP have very limited functionality. We may need custom protocol to satisfy the flexibility and extensibility requirements.

### Back-of-the-envelope estimation

- 1 billion users.
- Assume the average number of emails a person sends per day is 10. QPS for sending emails = $10^9$ x 10 / $10^5$ = 100,000.
- Assume the average number of emails a person receives in a day is 40 and the average email metadata is 50KB. (Metadata refers to everything related to an email, excluding attachment files.)
- Assume metadata is stored in a database. Storage requirements for maintaining metadata in 1 year: 1 billion users x 40 emails / day x 365 days x 50KB = 730 PB.
- Assume 20% of emails contain an attachment and the average attachment size is 500 KB.
- Storage for attachments in 1 year is: 1 billion users x 40 emails / day x 365 days x 20% x 500 KB = 1,460 PB.

## Step 2 - High-Level Design

### Email 101
