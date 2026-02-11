# 12. Design a Chat System

## Step 1 - Understand the problem and establish design scope

It is vital to agree on the type of chat app to design. In the marketplace, there are one-on-one chat apps like Facebook Messenger, WeChat, and WhatsApp, office chat apps that focus on group chat like Slack, or game chat apps, like Discord, that focus on large group interaction and low voice chat latency.

The first set of clarification questions should nail down what the interviewer has in mind exactly when he asks you to design a chat system.

__Candidate:__ What kind of chat app shall we design? 1 on 1 or group based?

__Interviewer:__ It should support both 1 on 1 and group chat.

__Candidate:__ Is this a mobile app? Or a web app? Or both?

__Interviewer:__ Both.

__Candidate:__ What is the scale of this app? A startup app or massive scale?

__Interviewer:__ It should support 50 million DAU.

__Candidate:__ For group chat, what is the group member limit?

__Interviewer:__ A maximum of 100 people

__Candidate:__ What features are important for the chat app? Can it support attachment?

__Interviewer:__ 1 on 1 chat, group chat, online indicator. The system only supports text messages.

__Candidate:__ Is there a message size limit?

__Interviewer:__ Yes, text length should be less than 100,000 characters long.

__Candidate:__ Is end-to-end encryption required?

__Interviewer:__

__Candidate:__ How long shall we store the chat history?

__Interviewer:__ Forever.

We focus on designing a chat app like Facebook messenger, with an emphasis on the following features:

- A one-on-one chat with low delivery latency
- Small group chat (max of 100 people)
- Online presence
- Multiple device support. The same account can be logged in to multiple devices at the same time.
- Push notifications

It is also important to agree on the design scale. We will design a system that supports 50 million DAU.

## Step 2 - Propose high-level design and get buy-in

### Client-Server communication

Clients do not directly communicate with each other. Instead, each client connects to a chat service. The chat service must support the following functions:

- Receive messages from other clients.
- Find the right recipients for each message and relay the message to the recipients.
- If a recipient is not online, hold the messages for that recipient on the server until he is online.

When a client intends to start a chat, it connects to the chat service using one or more network protocols.

When the sender sends a message to the receiver via the chat service, it can use the time-tested HTTP protocol. In this scenario, the client opens a HTTP connection with the chat service and sends the message, informing the service to send the message to the receiver. Keep-alive is efficient for this because keep-alive header allows a client to maintain a persistent connection with the chat service. It also reduces the number of TCP handshakes. HTTP is a fine option on the sender side, and many popular chat applications such as Facebook used HTTP initially to send messages.

The receiver side is a bit more complicated. Since HTTP is client-initiated, it is not trivial to send messages from the server. The techniques used to simulate a server-initiated connection are polling, long polling, and WebSocket.

#### Polling

Polling is a technique that the client periodically asks the server if there are messages available. Depending on polling frequency, polling could be costly. It could consume precious server resources to answer a question that offers no as an answer most of the time.

#### Long polling

Because polling could be inefficient, the next progression is long polling.

#### WebSocket

WebSocket is the most common solution for sending asynchronous updates from server to client.

### High-level design

#### Stateless Services

#### Stateful Service

#### Third-party integration

#### Scalability

#### Storage

#### Data models

## Step 3 - Design deep dive

## Step 4 - Wrap up
