# 11. Design a Newsfeed System

## Step 1 - Understand the problem and establish design scope

__Candidate:__ Is this a mobile app? Or a web app, or both?

__Interviewer:__ Both


__Candidate:__ What are the important features?

__Interviewer__: A user can publish a post and see their friends' posts on the newsfeed page.


__Candidate:__ Is the newsfeed sorted by reverse chronological order or any particular order such as topic scores? For instance, posts from your close friends have higher scores.

__Interviewer:__ To keep things simple, let's assume the feed is sorted in reverse chronological order.


__Candidate:__ How many friends can a user have?

__Interviewer:__ 5,000


__Candidate:__ What is the traffic volume?

__Interviewer:__ 10 million DAU


__Candidate:__ Can the feed contain images or videos?

__Interviewer:__ It can contain media files, including both images and videos.

## Step 2 - Propose high-level design and get buy-in

The design is divided into two flows: feed publishing and newsfeed building.

- Feed publishing: when a user publishes a post, corresponding data is written into cache and database. A post is populated to friends' newsfeed.
- Newsfeed building: for simplicity, let us assume the newsfeed is built by aggregating friends' posts in reverse chronological order.

### Newsfeed APIs

To publish a post.

`POST /v1/me/feed`

Params:

- `content`: content is the text of the post.
- `auth_token`: used to authenticate API requests.

To retrieve newsfeed:

`GET v1/me/feed`

Params:

- `auth_token`: used to authenticate API requests.

#### Feed publishing



#### Newsfeed building

## Step 3 - Design deep dive

## Step 4 - Wrap up
