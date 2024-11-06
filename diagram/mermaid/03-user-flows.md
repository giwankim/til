# 3. Visualize Application and User Flows

High-level interactions can be discussed with nontechnical colleagues to ensure what you're planning on building actually fulfuls the criteria of the project.

We can leverage a sequence diagram for this use case.

## Define Actors and Participants

All sequence diagrams must have actors and participants. An actor represents a human, and a participant represents a process such as a service or database.

```mermaid
---
title: User Sign Up Flow
---
sequenceDiagram
  actor Browser
  participant Sign Up Service
  participant User Service
  participant Kafka
```

## Add Interaction

```mermaid
---
title: User Sign Up Flow
---
sequenceDiagram
  actor Browser
  participant Sign Up Service
  participant User Service
  participant Kafka

  Browser->>Sign Up Service: GET /sign_up
  Sign Up Service-->>Browser: 200 OK (HTML page)
```

We use ->> for synchronous calls, which will show a solid line with a solid arrowhead. For reply messages, we use -->> which will show a dotted line with a solid arrowhead.

## Branching Logic

The majority of flows will at least have a happy path and an unhappy path.

Steer clear of detailing everything that could go wrong for every unhappy path, but detailing a few major elements of an unhappy path is helpful to identify the major area on which error handling should be focused.

```mermaid
---
title: User Sign Up Flow
---
sequenceDiagram
  actor Browser
  participant Sign Up Service
  participant User Service
  participant Kafka

  Browser->>Sign Up Service: GET /sign_up
  Sign Up Service-->>Browser: 200 OK (HTML page)

  Browser->>Sign Up Service: POST /sign_up
  Sign Up Service->>Sign Up Service: Validate input

  alt invalid input
    Sign Up Service-->>Browser: Error
  else valid input
    Sign Up Service->>User Service: POST /users
    User Service-->>Sign Up Service: 201 Created (User)
    Sign Up Service-->>Browser: 301 Redirect (Login page)
  end
```

Branching logic, known as alternative paths in Mermaid, look exactly like conditional statements from conventional programming languages.

## Asynchronous Messages

```mermaid
---
title: User Sign Up Flow
---
sequenceDiagram
  actor Browser
  participant Sign Up Service
  participant User Service
  participant Kafka

  Browser->>Sign Up Service: GET /sign_up
  Sign Up Service-->>Browser: 200 OK (HTML page)

  Browser->>Sign Up Service: POST /sign_up
  Sign Up Service->>Sign Up Service: Validate input

  alt invalid input
    Sign Up Service-->>Browser: Error
  else valid input
    Sign Up Service->>User Service: POST /users
    User Service--)Kafka: User Created Event Published
    User Service-->>Sign Up Service: 201 Created (User)
    Sign Up Service-->>Browser: 301 Redirect (Login page)
  end
```

Asynchronous messages are defined using --), which will render a dotted line with an empty arrowhead.

## Activations

## Notes
