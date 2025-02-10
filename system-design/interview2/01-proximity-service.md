# 1. Proximity Service

A proximity service is used to discover nearby places such as restaurants, hotels, theaters, museums, etc.

## Step 1 - Understand the Problem and Establish Design Scope

It's important to narrow down the scope by asking questions.

- Can a user specify the search radius? If there are not enough businesses within the search radius, does the system expand the search?
- What's the maximal radius allowed? Can I assume it's 20km?
- Can a user change the search radius on the UI?
- How does business information get added, deleted, or updated? Do we need to reflect these operations in real-time?
- A user might be moving while using the app/website. Do we need to refresh the page to keep the results up to date?

### Functional requirements

- Return all businesses based on a user's location (latitude and longitude pair) and radius.
- Business owners can add, delete, or update a business, but this information doesn't need to be reflected in real-time.
- Customers can view detailed information about a business.

### Non-functional requirements

- Low latency. User should be able to see nearby businesses quickly.
- Data privacy. Location info is sensitive data. When we design a location-based service (LBS), we should always take user privacy into consideration.
- High availability and scalability requirements. We should ensure our system can handle the spike in traffic during peak hours in densely populated areas.

### Back-of-the-envelope estimation

Assume we have 100 million daily active users and 200 million businesses.

#### Calculate QPS

- Seconds in a day = $24 * 60 * 60 = 86,400$. We can round it up to $10^5$ for easier calculation.
- Assume a user makes 5 search queries per day.
- Search QPS = $100 million * 5 / 10^5$
