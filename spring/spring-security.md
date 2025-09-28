# Spring Security

Provides authentication, authorization, and protection against common attacks. Supports both
imperative and reactive applications.

## Features

### Authentication

Authentication is how we verify the identity of who is trying to access a particular resource.

### Authorization

Authorization is determining who is allowed to access a particular resource. Spring Security
provides defense in depth by allowing for request based authorization and method based
authorization.

### Protection Against Exploits

#### Cross Site Request Forgery (CSRF)

## Servlet Applications

### Architecture

#### Filters

```mermaid
---
title: FilterChain
---
flowchart TB
%% Nodes
    C([Client])

    subgraph FC[FilterChain]
        direction TB
        F0[[Filter_0]]
        F1[[Filter_1]]
        F2[[Filter_2]]
        S[[Servlet]]
    end

%% Request path
    C <--> F0
    F0 <--> F1 <--> F2 <--> S

%% Styling (optional)
    classDef filter fill:#f57c00, stroke:#333, color:#fff;
    classDef client fill:#7e57c2, stroke:#333, color:#fff;
    class F0,F1,F2,S filter
    class C client
    style FC fill: #ffffff00, stroke: #555, stroke-dasharray: 5 5;
```

Client sends a request to the application, and the servlet container creates a `FilterChain`, which contains the `Filter` instances and the `Servlet` that should process the `HttpServletRequest`, based onn the path of the request URI. In a Spring MVC application, the `Servlet` is an instance of `DispatcherServlet`

Since a `Filter` impacts only downstream `Filter` instances and the `Servlet`, the order in which each `Filter` is invoked is extremely important.

#### DelegatingFilterProxy

Spring provides a `Filter` implementation named `DelegatingFilterProxy` that allows bridging between the Servlet container's lifecycle and Spring's `ApplicationContext`. You can register `DelegatingFilterProxy` through the standard Servlet container mechanisms but delegate all the work to a Spring Bean that implements `Filter`.

`DelegatingFilterProxy` allows delaying looking up `Filter` bean instances. Container needs to register the `Filter` instances before the container can start up; however, Spring typically uses a `ContextLoaderListener` to load the Spring Beans, which is not done until after the `Filter` instances need to be registered. 

#### FilterChainProxy

Spring Security's Servlet support is contained within `FilterChainProxy`. `FilterChainProxy` is a special `Filter` provided by Spring Security that allows delegating to many `Filter` instances through `SecurityFilterChain`. Since `FilterChainProxy` is a Bean, it is typically wrapped in a DelegatingFilterProxy.

#### SecurityFilterChain

`SecurityFilterChain` is used by *FilterChainProxy* to determine which Spring Security Filter instances should be invoked for the current request.

```mermaid
graph TB
    Client["Client"]

    Client <--> FC

    subgraph FC["FilterChain"]
        Filter0["Filter₀"]

        subgraph DFP["DelegatingFilterProxy"]
            FCP["FilterChainProxy"]
        end

        Filter2["Filter₂"]
        Servlet["Servlet"]

        Filter0 <--> DFP
        DFP <--> Filter2
        Filter2 <--> Servlet
    end

    FCP --> SFC

    subgraph SFC["SecurityFilterChain"]
        direction TB
        SF0["Security Filter₀"]
        SF1["Filter"]
        SF2["Filter"]
        SF3["Filter"]
        SFn["Security Filter_n"]

        SF0 --> SF1
        SF1 --> SF2
        SF2 --> SF3
        SF3 --> SFn
    end

    style Client fill:#b19cd9
    style Filter0 fill:#ff7f50
    style DFP fill:#ff7f50,stroke:#333,stroke-width:2px
    style FCP fill:#fff,stroke:#000
    style Filter2 fill:#ff7f50
    style Servlet fill:#ff7f50
    style SF0 fill:#ff7f50
    style SF1 fill:#ff8c69
    style SF2 fill:#ff8c69
    style SF3 fill:#ff8c69
    style SFn fill:#ff7f50
```

The Security Filters in `SecurityFilterChain` are typically Beans, but they are registered with `FilterChainProxy` instead of `DelegatingFilterProxy`.

#### Security Filters

#### Handling Security Exceptions

#### Logging
