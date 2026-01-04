# Dockerfiles

Dockerfile이나 Cloud Native Buildpacks를 사용해서 최적화된 docker 호환 컨테이너 이미지로 만들 수 있다.

## Efficient Docker Images

다음과 같이 간단하게 스프링 부트 uber jar를 사용한 도커 이미지를 정의할 수 있다.

```dockerfile
FROM eclipse-temurin:21
WORKDIR workspace
ARG JAR_FILE=build/libs/*.jar
COPY ${JAR_FILE} catalog-service.jar
ENTRYPOINT ["java", "-jar", "catalog-service.jar"]
```

하지만 컨테이너 환경에서 압축된 uber jar를 그대로 사용하는 것은 성능에 안 좋은 영향을 끼칠 수 있고, 작성한 애플리케이션 코드와 모든 의존성들을 도커 이미지의 동일한
레이어에 포함시키는 것은 이미지를 비효율적으로 만든다. 일반적으로 스프링 부트 버전을 업그레이드하거나 새로운 의존성을 추가하는 빈도수가 새로운 애플리케이션 클래스를 구현하는
빈도수보다 낮기 때문에 이런 경향을 도커 이미지에 반영하는 것이 좋다. Jar 파일들을 애플리케이션 구현 클래스들 전 레이어에 포함시킬 경우, 도커는 가장 밑의 레이어만 변경하면
되고 나머지는 캐시에서 가져다 쓰면 된다.

### Layering Docker images

더 효율적인 도커 이미지를 만드는 것을 지원하기 위해서 스프링 부트는 jar 파일에 레이어 인덱스 파일을 추가한다. 스프링 부트 2.3부터 layered-JAR mode를 지원했고
2.4 버전부터는 디폴트로 설정되어 있다. 인덱스 파일에 레이어 리스트 순서는 레이어들이 Docker/OCI 이미지에 포함될 순서와 동일하다. 기본적으로 다음과 같은 레이어들이
포함된다.

- `dependencies` (regular released dependencies)
- `spring-boot-loader` (class used by the Spring Boot loader component, everything under
  `org/springframework/boot/loader`)
- `snapshot-dependencies` (snapshot dependencies)
- `application` (application classes and resources)

`layer.idx` 파일의 예는 다음과 같다.

```yaml
- "dependencies":
    - BOOT-INF/lib/library1.jar
    - BOOT-INF/lib/library2.jar
- "spring-boot-loader":
    - org/springframework/boot/loader/launch/JarLauncher.class
    - ... <other classes>
- "snapshot-dependencies":
    - BOOT-INF/lib/library3-SNAPSHOT.jar
- "application":
    - META-INF/MANIFEST.MF
    - BOOT-INF/classes/a/b/C.class
```

## Dockerfile

레이어 인덱스 파일이 포함된 jar 파일 빌드 시, `spring-boot-jarmode-tools` jar가 디펜던시로 jar에 포함된다.

`tools` jar mode로 jar 파일을 실행하면

```shell
java -Djarmode=tools -jar app.jar
```

출력이 다음과 같다.

```
Usage:
  java -Djarmode=tools -jar my-app.jar

Available commands:
  extract      Extract the contents from the jar
  list-layers  List layers from the jar that can be extracted
  help         Help about any command
```

`extract` 명령을 사용해서 `Dockerfile`에 순차적으로 더할 레이어들을 추출할 수 있다.

```dockerfile
FROM bellsoft/liberica-openjre-debian:24-cds AS builder
WORKDIR /builder
ARG JAR_FILE=build/libs/*.jar
COPY ${JAR_FILE} application.jar
RUN java -Djarmode=tools -jar application.jar extract --layers --destination extracted

FROM bellsoft/liberica-openjre-debian:24-cds
WORKDIR /application
COPY --from=builder /builder/extracted/dependencies/ ./
COPY --from=builder /builder/extracted/spring-boot-loader/ ./
COPY --from=builder /builder/extracted/snapshot-dependencies/ ./
COPY --from=builder /builder/extracted/application/ ./
ENTRYPOINT ["java", "-jar", "application.jar"]
```

Multi-stage `Dockerfile`이고 builder 단계가 다음 단계에서 사용할 디렉터리들을 추출한다. 각 `COPY` 명령이 jarmode를 통해서 추출한 레이어와 연관되어 있다.
