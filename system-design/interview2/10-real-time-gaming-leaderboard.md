# 10 Real-time Gaming Leaderboard

Design a leaderboard for an online mobile game.

![leaderboard](../../assets/system-design/interview2/leaderboard.png)

## Step 1 - Establish Design Scope

### Functional requirements

- Display top 10 players on the leaderboard.
- Show a user's specific rank.
- Display players who are four places above and below the desired user (bonus).

### Non-functional requirements

- Real-time update on scores.
- Score update is reflected on the leaderboard in real-time.
- General scalability, availability, and reliability requirements.

### Back-of-the-envelope estimation

- 5 million DAU
- If the game had an even distribution of players during a 24-hour period, would have an average of 50 users per second (5,000,000 DAU / $10^5$ seconds = ~50).
- Assume peak load would be 5 times the average. Peak load of 250 users per second.

QPS for users scoring a point: if a user plays 10 games per day on average, the QPS for users scoring a point is 50 * 10 = ~500. Peak QPS is 5x the average: 500 * 5 = 2,500.

QPS for fetching the top 10 leaderboard: assume a user opens the game once per day and the top 10 leaderboard is loaded onlyh when a user first opens the game. QPS for this is around 50.

## Step 2 - High-Level Design

### API design

### High-level architecture

### Data models
