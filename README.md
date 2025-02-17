# Today I Learned

## Books

- [The Pragmatic Programmer](books/tpp.md)

## Build Tool

- Gradle
  - Running Gradle Builds
    - [Learning the Basics](build-tool/gradle/running-builds/basics.md)
  - Authoring Gradle Builds
    - [Learning the Basics](build-tool/gradle/authoring-builds/basics.md)

## CS

- TODO: Two's complement
  - [Finally getting two's complement](https://neugierig.org/software/blog/2023/06/twos-complement.html)
  - [Two's Complement](https://www.cs.cornell.edu/~tomf/notes/cps104/twoscomp.html)
- TODO: Master theorem
- TODO: [Floating point visually explained](https://fabiensanglard.net/floating_point_visually_explained/)
- TODO: [WebSockets vs Server-Sent-Events vs Long-Polling (vs WebRTC vs WebTransport)](https://rxdb.info/articles/websockets-sse-polling-webrtc-webtransport.html)
- TODO: [Distributed Locking](https://martin.kleppmann.com/2016/02/08/how-to-do-distributed-locking.html)
- TODO: [B-trees and database indexes](https://planetscale.com/blog/btrees-and-database-indexes)
- TODO: Transaction isolation
  - [Transaction Isolation in Postgres explained](https://www.thenile.dev/blog/app/blog/transaction-isolation-postgres)
- TODO: Consistent Hashing
  - [Stanford lecture notes](https://web.stanford.edu/class/cs168/l/l1.pdf)
  - [simple Java implementation](https://tom-e-white.com/2007/11/consistent-hashing.html)
- TODO: [Caching strategies](https://codeahoy.com/2017/08/11/caching-strategies-and-how-to-choose-the-right-one/)
- TODO: OAuth
  - [OAuth from First Principles](https://stack-auth.com/blog/oauth-from-first-principles)
  - [OAuth 원리와 이해 (feat. 카카오 로그인)](https://yejipro.tistory.com/entry/OAuth-%EC%9B%90%EB%A6%AC%EC%99%80-%EC%9D%B4%ED%95%B4-feat-%EC%B9%B4%EC%B9%B4%EC%98%A4-%EB%A1%9C%EA%B7%B8%EC%9D%B8)

## Diagram

- [Creating Sofware with Modern Diagramming Techniques](https://pragprog.com/titles/apdiag/creating-software-with-modern-diagramming-techniques/)
  - [0. Introduction](diagram/mermaid/00-intro.md)
  - [1. Document your Domain](diagram/mermaid/01-document-domain.md)
  - [2. Enhance your Domain Model](diagram/mermaid/02-enhance-domain.md)
  - [3. Visualize Application and User Flows](diagram/mermaid/03-user-flows.md)

## Java

- Effective Java
  - [Item 24: Favor static member classes over nonstatic](java/effective-java/04-classes-interfaces/item24.md)

## JPA

- [JPA의 사실과 오해](https://event-us.kr/choyoungho/event/98186)
  - [1일차 JPA와 아키텍처](jpa/사실과오해/orm-and-architecture)
  - [2일차 고급 설계 이슈](jpa/사실과오해/2-advanced-design-issues.md)

- [Java Persistence with Spring Data and Hibernate](https://www.manning.com/books/java-persistence-with-spring-data-and-hibernate)
  - Part 2: Mapping strategies
    - [5. Mapping persistent classes](jpa/java-persistence/05-mapping-persistent-classes.md)
    - [7. Mapping inheritance](jpa/java-persistence/07-mapping-inheritance.md)
  - Part 3: Transactional data processing
    - [10. Managing data](jpa/java-persistence/10-managing-data.md)
    - [11. Transactions and concurrency](jpa/java-persistence/11-transactions-and-concurrency.md)

## Kotlin

- [Kotlin in Action](https://www.manning.com/books/kotlin-in-action-second-edition)
  - [1. Kotlin: What and why](kotlin/kia/01-what-and-why.md)
  - [2. Kotlin basics](kotlin/kia/02-basics.md)
  - [3. Defining and calling functions](kotlin/kia/03-functions.md)
  - [4. Classes, objects, and interfaces](kotlin/kia/04-classes.md)
  - [5. Programming with lambdas](kotlin/kia/05-lambdas.md)
  - [13. DSL construction](kotlin/kia/13-dsl.md)
- [Effective Kotlin](https://kt.academy/book/effectivekotlin)
  - [Item 36: Prefer composition over inheritance](kotlin/effective-kotlin/36-composition-over-inheritance.md)

## PS

- [컬렉션 프레임워크](ps/collections.md)

## Redis

- [개발자를 위한 레디스](http://www.acornpub.co.kr/book/redis_for_developers)
  - [1장 마이크로서비스 아키텍처와 레디스](redis/redis-for-developer/01-msa.md)

## Refactoring

- [토비님의 리팩토링 읽기 모임](https://discord.com/channels/687618003717587011/1327448191091867783)
  - [Refactoring: Improving the Design of Existing Code](https://martinfowler.com/books/refactoring.html)
    - [1 - Refactoring: A First Example](https://github.com/giwankim/refactoring/blob/main/docs/chapter01.md)
    - [2 - Principles in Refactoring](https://github.com/giwankim/refactoring/blob/main/docs/chapter02.md)
    - [3 - Bad Smells in Code](https://github.com/giwankim/refactoring/blob/main/docs/chapter03.md)
    - [4 - Building Tests](https://github.com/giwankim/refactoring/blob/main/docs/chapter04.md)

## Spring

## System Design

- [System Design Interview](https://www.amazon.com/System-Design-Interview-insiders-Second/dp/B08CMF2CQF)
  - [3. A Framework for System Design Interviews](system-design/interview/03-framework.md)
  - [4. Design a Rate Limiter](system-design/interview/04-rate-limiter.md)
  - [5. Design a Consistent Hashing](system-design/interview/05-consistent-hash.md)
  - [6. Design a Key-Value Store](system-design/interview/06-key-value.md)
  - [7. Design a Unique ID Generator in Distributed Systems](system-design/interview/07-id-generator.md)
  - [8. Design a URL Shortener](system-design/interview/08-url-shortener.md)
  - [9. Design a Web Crawler](system-design/interview/09-web-crawler.md)
  - [10. Design a Notification System](system-design/interview/10-notification-system.md)
  - [11. Design a News Feed System](system-design/interview/11-news-feed-system.md)
  - [12. Design a Chat System](system-design/interview/12-chat-system.md)

- [System Design Interview: Volume 2](https://www.amazon.com/dp/1736049119)
  - [1. Proximity Service](system-design/interview2/01-proximity-service.md)
  - [2. Nearby Friends](system-design/interview2/02-nearby-friends.md)
  - [3. Google Maps](system-design/interview2/03-google-maps.md)

## 잡담

- [원영적 사고와 에자일](etc/lucky-vicky.md)
