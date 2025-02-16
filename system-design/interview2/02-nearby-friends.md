# 2. Nearby Friends

Mobile app feature called "Nearby Friends". Mobile client presents a list of friends who are geographically nearby.

In proximity services, the addresses for businesses are static, while in "nearby friends" data is more dynamic.

## Step 1 - Understand the Problem and Establish Design Scope

### Functional requirements

- Users should be able to see nearby friends. Each entry in the nearby friend list has a distance and timestamp indicating when the distance was last updated.
- Nearby friend lists should be updated every few seconds.

### Non-functional requirements

- Low latency.
- Reliability.
- Eventual consistency.

### Back-of-the-envelope estimation

- Nearby friends are defined as friends whose locations are within a 5-mile radius.
- Location refresh interval is 30 seconds. (Human walking speed is slow. The distance traveled in 30 seconds does not make a significant difference on the "nearby friends" feature).
- On average, 100 million users use the feature every day.
- Assume the number of concurrent users is 10% of DAU, so the number of concurrent users is 10 million.
- On average, a user has 400 friends. Assume all of them use the "nearby friends" feature.
- App displays 20 nearby friends per page and may load more nearby friends upon request.

### Calculate QPS

- 100 million DAU
- Concurrent users: 10% * 100 million = 10 million
- Users report their locations every 30 seconds
- Location update QPS = 10 million / 30 = ~334,000

## Step 2 - Propose High-Level Design and Get Buy-In

### High-level design

What are the responsibilities of the backend?

- Receive location updates from all active users.
- For each location update, find all the active friends who should receive it and forward it to those users' devices.
