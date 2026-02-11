# Architecture

## Filters

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

Client sends a request to the application, and the servlet container creates a `FilterChain`, which
contains the `Filter` instances and the `Servlet` that should process the `HttpServletRequest`,
based on the path of the request URI. In a Spring MVC application, the `Servlet` is an instance of
`DispatcherServlet`.

Since a `Filter` impacts only downstream `Filter` instances and the `Servlet`, the order in which
each `Filter` is invoked is extremely important.

### DelegatingFilterProxy

Spring provides a `Filter` implementation named `DelegatingFilterProxy` that allows bridging between
the Servlet container's lifecycle and Spring's `ApplicationContext`. You can register
`DelegatingFilterProxy` through the standard Servlet container mechanisms but delegate all the work
to a Spring Bean that implements `Filter`.

`DelegatingFilterProxy` allows delaying the lookup of `Filter` bean instances. Container needs to
register the `Filter` instances before the container can start up; however, Spring typically uses a
`ContextLoaderListener` to load the Spring Beans, which is not done until after the `Filter`
instances need to be registered.

### FilterChainProxy

Spring Security's Servlet support is contained within `FilterChainProxy`. `FilterChainProxy` is a
special `Filter` provided by Spring Security that allows delegating to many `Filter` instances
through `SecurityFilterChain`. Since `FilterChainProxy` is a Bean, it is typically wrapped in a
DelegatingFilterProxy.

### SecurityFilterChain

`SecurityFilterChain` is used by *FilterChainProxy* to determine which Spring Security Filter
instances should be invoked for the current request.

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

    style Client fill: #b19cd9
    style Filter0 fill: #ff7f50
    style DFP fill: #ff7f50, stroke: #333, stroke-width: 2px
    style FCP fill: #fff, stroke: #000
    style Filter2 fill: #ff7f50
    style Servlet fill: #ff7f50
    style SF0 fill: #ff7f50
    style SF1 fill: #ff8c69
    style SF2 fill: #ff8c69
    style SF3 fill: #ff8c69
    style SFn fill: #ff7f50
```

The Security Filters in `SecurityFilterChain` are typically Beans, but they are registered with
`FilterChainProxy` instead of `DelegatingFilterProxy`. The advantages of using `FilterChainProxy` to
registering directly with the Servlet container or DelegatingFilterProxy are:

1. It provides a starting point for all of Spring Security's Servlet support. For that reason, when
   troubleshooting, adding a debug point in `FilterChainProxy` is a great place to start.
2. Since `FilterChainProxy` is central to Spring Security usage, it can perform tasks that are not
   viewed as optional. For example, it clears out the `SecurityContext` to avoid memory leaks. Also
   applies `HttpFirewall` to protect against certain types of attacks.
3. In a Servlet container, `Filter` instances are invoked based upon the URL alone.
   `FilterChainProxy` can determine invocation based upon anything in the `HttpServletRequest` by
   using the `RequestMatcher` instance.

The following image shows multiple `SecurityFilterChain` instances:

```mermaid
graph TD
    Client[Client]

    subgraph FilterChain
        direction TB
        Filter0[Filter₀]

        subgraph DFP["DelegatingFilterProxy"]
            FCP["FilterChainProxy"]
        end

        Filter2[Filter₂]
        Servlet[Servlet]

        Filter0 <--> DFP
        DFP <--> Filter2
        Filter2 <--> Servlet
    end

    Decision{?}

    subgraph SecurityFilterChain0["SecurityFilterChain₀"]
        direction TB
        API["/api/**"]
        SF0_1[ ]
        SF0_2[ ]
        SF0_3[ ]

        API --- SF0_1
        SF0_1 --- SF0_2
        SF0_2 --- SF0_3
    end

    subgraph SecurityFilterChainN["SecurityFilterChain_n"]
        direction TB
        Root["/**"]
        SFN_1[ ]
        SFN_2[ ]
        SFN_3[ ]
        SFN_4[ ]

        Root --- SFN_1
        SFN_1 --- SFN_2
        SFN_2 --- SFN_3
        SFN_3 --- SFN_4
    end

    Client <--> Filter0
    DFP --> Decision
    Decision --> SecurityFilterChain0
    Decision --> SecurityFilterChainN

    style Filter0 fill:#ff8c42
    style DFP fill:#ff8c42,stroke:#333,stroke-width:2px
    style FCP fill:#fff,stroke:#000
    style Filter2 fill:#ff8c42
    style Servlet fill:#ff8c42
    style SF0_1 fill:#ff8c42
    style SF0_2 fill:#ff8c42
    style SF0_3 fill:#ff8c42
    style SFN_1 fill:#ff8c42
    style SFN_2 fill:#ff8c42
    style SFN_3 fill:#ff8c42
    style SFN_4 fill:#ff8c42
```

### Security Filters

SecurityFilters are inserted into the *FilterChainProxy* with the *SecurityFilterChain* API. Those filters can be used for a number of different purposes, like exploit protection, authentication, authorization, and more.

Security filters are most often declared using an `HttpSecurity` instance.

```kotlin
@Configuration
@EnableWebSecurity
class SecurityConfig {
    @Bean
    fun filterChain(http: HttpSecurity): SecurityFilterChain {
        http {
            csrf { }
            httpBasic { }
            formLogin { }
            authorizeHttpRequests {
                authorize(anyRequest, authenticated)
            }
        }
        return http.build()
    }
}
```

The above configuration will result in the following `Filter` ordering:

| Filter                      | Added by                             |
|-----------------------------|--------------------------------------|
| CsrfFilter                  | `HttpSecurity#csrf`                  |
| BasicAuthenticationFilter   | `HttpSecurity#httpBasic`             |
| FormLoginFilter             | `HttpSecurity#formLogin`             |
| AuthorizeHttpRequestsFilter | `HttpSecurity#authorizeHttpRequests` |

#### Printing the Security Filters

The list of filters is printed at DEBUG level on the application startup, so you can see something
like the following:

```text
2023-06-14T08:55:22.321-03:00  DEBUG 76975 --- [           main] o.s.s.web.DefaultSecurityFilterChain     : Will secure any request with [ DisableEncodeUrlFilter, WebAsyncManagerIntegrationFilter, SecurityContextHolderFilter, HeaderWriterFilter, CsrfFilter, LogoutFilter, UsernamePasswordAuthenticationFilter, DefaultLoginPageGeneratingFilter, DefaultLogoutPageGeneratingFilter, BasicAuthenticationFilter, RequestCacheAwareFilter, SecurityContextHolderAwareRequestFilter, AnonymousAuthenticationFilter, ExceptionTranslationFilter, AuthorizationFilter]
```

### Handling Security Exceptions

The `ExceptionTranslationFilter` allows translation of `AccessDeniedException` and
`AuthenticationException` into HTTP responses.

`ExceptionTranslationFilter` is inserted into the `FilterChainProxy` as one of the Security Filters.

```mermaid
flowchart LR
    %% ---------- Styles ----------
    classDef orange fill:#f7a86a,stroke:#b25a13,color:#000;
    classDef blue fill:#162d6b,stroke:#0b1b49,color:#fff;
    classDef mini fill:#efefef,stroke:#bdbdbd,color:#333;

    %% ---------- Security Filter Chain (left) ----------
    subgraph SFC[SecurityFilterChain]
    direction TB
        pre1[ ]:::mini
        pre2[ ]:::mini
        ETF[ExceptionTranslationFilter]:::orange
        post1[ ]:::mini
        post2[ ]:::mini
        pre1 <--> pre2 <--> ETF <--> post1 <--> post2
    end
    style SFC stroke:#9a9a9a,stroke-dasharray:5 5, rx: 6, ry: 6;

    %% ---------- Decision & outcomes (right side) ----------
    ETF -- ① --> S1[Continue Processing<br/>Request Normally]
    S1 -- Security Exception --> DEC{ <br/> }

    %% ② Start Authentication (bottom-left block)
    DEC -- ② --> SA
    subgraph SA[Start Authentication]
    direction TB
        SCH[SecurityContextHolder]:::mini
        RC[RequestCache]:::mini
        EP[AuthenticationEntryPoint]:::mini
        SCH --- RC --- EP
    end
    style SA stroke: #9a9a9a, stroke-dasharray: 5 5, rx: 6, ry: 6;
    linkStyle 7,8 stroke:transparent;

    %% ③ Access Denied (bottom-right block)
    subgraph ADH[Access Denied]
    direction TB
        ADE[AccessDeniedHandler]:::blue
    end
    DEC -- ③ --> ADH
```

- ①
- ②
- ③

### Logging

Spring Security provides logging of all security related events at the DEBUG and TRACE level. This
can be very useful for debugging your application.

Consider an example where a user tries to make a `POST` request to a resource that has CSRF
protection enabled without the CSRF token. With no logs, the user will see a 403 error with no
explanation of why the request was rejected. However, if you enable logging for Spring Security, you
will see a log message like this:

```text
2023-06-14T09:44:25.797-03:00 DEBUG 76975 --- [nio-8080-exec-1] o.s.security.web.FilterChainProxy        : Securing POST /hello
2023-06-14T09:44:25.797-03:00 TRACE 76975 --- [nio-8080-exec-1] o.s.security.web.FilterChainProxy        : Invoking DisableEncodeUrlFilter (1/15)
2023-06-14T09:44:25.798-03:00 TRACE 76975 --- [nio-8080-exec-1] o.s.security.web.FilterChainProxy        : Invoking WebAsyncManagerIntegrationFilter (2/15)
2023-06-14T09:44:25.800-03:00 TRACE 76975 --- [nio-8080-exec-1] o.s.security.web.FilterChainProxy        : Invoking SecurityContextHolderFilter (3/15)
2023-06-14T09:44:25.801-03:00 TRACE 76975 --- [nio-8080-exec-1] o.s.security.web.FilterChainProxy        : Invoking HeaderWriterFilter (4/15)
2023-06-14T09:44:25.802-03:00 TRACE 76975 --- [nio-8080-exec-1] o.s.security.web.FilterChainProxy        : Invoking CsrfFilter (5/15)
2023-06-14T09:44:25.814-03:00 DEBUG 76975 --- [nio-8080-exec-1] o.s.security.web.csrf.CsrfFilter         : Invalid CSRF token found for http://localhost:8080/hello
2023-06-14T09:44:25.814-03:00 DEBUG 76975 --- [nio-8080-exec-1] o.s.s.w.access.AccessDeniedHandlerImpl   : Responding with 403 status code
2023-06-14T09:44:25.814-03:00 TRACE 76975 --- [nio-8080-exec-1] o.s.s.w.header.writers.HstsHeaderWriter  : Not injecting HSTS header since it did not match request to [Is Secure]
```

To log all the security events, you can add the following:

```properties
logging.level.org.springframework.security=TRACE
```

```xml

<configuration>
  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <!-- ... -->
  </appender>
  <!-- ... -->
  <logger name="org.springframework.security" level="trace" additivity="false">
    <appender-ref ref="Console"/>
  </logger>
</configuration>
```
