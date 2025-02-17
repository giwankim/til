# 3. Google Maps

Google started Project Google Maps in 2005 and developed a web mapping service. It provides many services such as satellite imagery, street maps, real-time traffic conditions, and route planning.

Google Maps helps users find directions and navigate to their destination. As of March 2021, Google Maps had one billion DAU, 99% coverage of the world, and 25 million updates daily of accurate and real-time information.

## Step 1 - Understand the Problem and Establish Design Scope

We focus on three key features. The main devices that we need to support are mobile phones.

- User location update.
- Navigation service, including ETA service.
- Map rendering.

### Non-functional requirements and constraints

- Accuracy: Users should not be given wrong directions.
- Smooth navigation: On
- Data and battery usage:
- General availability and scalability

### Map 101

#### Positioning system

#### Going from 3D to 2D

#### Geocoding

#### Geohashing

#### Map rendering

#### Road data processing for navigation algorithms

### Back-of-the-envelope estimation

#### Storage demand

#### Server throughput

## Step 2 - Propose High-Level Design and Get Buy-In

### High-level design

![high level design](../../assets/system-design/interview2/google-maps-high-level-design.png)

### Location service

The location service is responsible for recording a user's location update.

### Navigation service

Responsible for finding a fast route from point A to point B. We can tolerate a bit of latency. Calculated route does not have to be the fastest, but accuracy is critical.

The request includes origin and destination as the parameters.

```http
GET /v1/nav?origin=1355+market+street,SF&destination=Disneyland
```

### Map rendering

The map tiles must be fetched on-demand from the server based on the client's location and the zoom level of the client viewport.

#### Option 1

Server builds the map tiles on the fly, based on the client location and zoom level of the client viewport.

Disadvantages:

- It puts a huge load on the server cluster to generate every map tile dynamically.
- Since the map tiles are dynamically generated, it is hard to take advantage of caching.

#### Option 2
