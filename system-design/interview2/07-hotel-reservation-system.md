# 7. Hotel Reservation System

The design and techniques used in this chapter are also applicable to other popular booking-related interview topics:
- Design Airbnb
- Design a flight reservation system
- Design a movie ticket booking system

## Step 1 - Understand the Problem and Establish Design Scope

### Functional requirements

- Show the hotel-related page.
- Show the hotel room-related detail page.
- Reserve a room.
- Admin panel to add/remove/update hotel or room info.
- Support the overbooking feature.

### Non-functional requirements

- High concurrency. During peak season or big events, may have a lot of customers trying to book the same room.
- Moderate latency.

### Back-of-the-envelope estimation

- 5000 hotels and 1 million rooms in total.
- Assume 70% of the rooms are occupied and the average stay duration is 3 days.
- Daily reservations: (1 million * 0.7) / 3 = 233,333 (~240,000)
- Reservations per second = $240,000 / 10^5$ seconds in a day = ~3.

Rough calculation of the QPS of all pages in the system.

1. View hotel/room detail page.
2. View the booking page.
3. Reserve a room.

![qps](../../assets/system-design/interview2/booking-qps.png)

## Step 2 - High-Level Design

### API design

#### Hotel-related APIs

| API | Detail |
| --- | ------ |
| GET /v1/hotels/{id} | Get detailed information about a hotel |
| POST /v1/hotels | Add a new hotel. (Only available to hotel staff) |
| PUT /v1/hotels/{id} | Update hotel information. (Only available to hotel staff) |
| DELETE /v1/hotels/{id} | Delete a hotel. (Only available to hotel staff) |


#### Room-related APIs

| API | Detail |
| --- | ------ |
| GET /v1/hotels/{hotelID}/rooms/{id} | Get detailed information about a room |
| POST /v1/hotels/{hotelId}/rooms | Add a room. (Only available to hotel staff) |
| PUT /v1/hotels/{hotelId}/rooms | Update room information. (Only available to hotel staff) |
| DELETE /v1/hotels/{hotelId}/rooms | Delete a room. (Only available to hotel staff) |

#### Reservation related APIs

| API | Detail |
| --- | ------ |
| GET /v1/reservations | Get the reservation history of the logged-in user |
| GET /v1/reservations/{id} | Get detailed information about a reservation |
| POST /v1/reservations | Make a new reservation |
| DELETE /v1/reservations | Cancel a reservation |

Request parameters of making a new reservation:

```json
{
  "startDate": "2021-04-28",
  "endDate": "2021-04-30",
  "hotelID": 245,
  "roomID": "U12345673389",
  "reservationID": "U12354673390"
}
```

`reservationID` is used as the idempotency key to prevent double booking.

### Data model

### High-level design

![msa](../../assets/system-design/interview2/hotel-reservation-msa.png)
