# 6. Ad Click Event Aggregation

Digital advertising has a core process called Real-Time Bidding (RTB), in which digital advertising inventory is bought and sold.

![real-time bidding](../../assets/system-design/interview2/rtb.png)

Ad click event aggregation plays a critical role in measuring the effectiveness of online advertising. The key metrics used in online advertising, including click-through rate and conversion rate, depend on aggregated ad click data.

## Step 1 - Establish Design Scope

### Functional requirements

- Aggregate the number of clicks of `ad_id` in the last M minutes.
- Return the top 100 most clicked `ad_id` every minute.
- Support aggregation filtering by different attributes (`ip`, `user_id`, `country`).
- Dataset volume is at Facebook or Google scale.

### Non-functional requirements

- Correctness is important as the data is used for RTB and ads billing.
- Properly handle delayed or duplicate events.
- Robustness. Resilient to partial failures.
- Latency requirement. End-to-end latency should be few minutes, at most.

### Back-of-the-envelope estimation

- 1 billion DAU
- Assume on average each user clicks 1 ad per day. That's 1 billion ad click events per day.
- Ad click QPS = $10^9$ events / $10^5$ seconds in a day = 10,000
- Assume peak ad click QPS is x5 the average number. Peak QPS = 50,000.
- Assume a single ad click event occupies 0.1 KB storage. Daily storage requirement is 0.1KB * 1 billion = 100 GB. The monthly storage requirement is about 3 TB.

## Step 2 - Propose High-Level Design

### Query API design

### Data model

### High-level design
