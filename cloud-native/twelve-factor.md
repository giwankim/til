# The Twelve-Factor App

## 배경

요즘 [클라우드 네이티브 스프링 인 액션](https://www.manning.com/books/cloud-native-spring-in-action)을 읽고 있다. 책의 작은
마이크로서비스
예제는 [Beyond the Twelve-Factor App](https://learning.oreilly.com/library/view/beyond-the-twelve-factor/9781492042631/)
에서 소개한 15가지 클라우드 네이티브 방법론을 따르고 있다. 예제를 보다 보면 [Spring Cloud](https://spring.io/projects/spring-cloud)
프로젝트들이 이 방법론을 매우 잘 지원한다는 느낌을 받는다. 찾아보니 Beyond the Twelve-Factor App의 저자는 당시 Pivotal의 컨설턴트였고, 이 책은
Pivotal 웹사이트를 통해 무료로 제공됐던 책이다. 그러니 어느 정도 당연한 결과일지도 모르겠다.

우선 이름은 많이 들어봤지만 정작 자세히 읽어본 적은 없는 [Twelve-Factor App](https://12factor.net/ko/)부터 읽고, 나중에 참고하기 좋게 간단히
정리해 보기로 했다.

## The Twelve-Factor App

The twelve-factor app is a methodology for building SaaS apps that:

- use **declarative** formats for setup automation,
- have **clean contract** with underlying OS (maximum portability);
- are **suitable for deployment** on cloud platforms,
- **minimize divergence** between dev and prod, enabling continuous deployment;
- and can **scale up** without significant changes.

### I. Codebase
#### One codebase tracked in revision control, many deploys

### II. Dependencies

### XI. Logs
#### Treat logs as event streams
