# Getting Started

## Spring Boot

Spring Boot provides `spring-boot-starter-security` starter that aggregates Spring Security-related
dependencies.

The default arrangement of Spring Boot and Spring Security affords the following behaviors at
runtime:

- Requires an authenticated user for any endpoint (including `/error` endpoint)
- Registers a default user with a generated password at startup
- Protects password storage with BCrypt
- Provides form-based login and logout flows
- Authenticates form-based login as well as HTTP Basic
- Provides content negotiation; for web request, redirect to the login page; for service requests,
  returns a `401 Unauthorized`
- Mitigates CSRF attacks
- Mitigates Session Fixation attacks
- Writes Strict-Transport-Security to ensure HTTPS
- Writes X-Content-Type-Options to mitigate sniffing attacks
- Writes Cache Control headers that protect authenticated resources
- Writes X-Frame-Options to mitigate Clickjacking
- Integrates with `HttpServletRequest`'s authentication methods
- Publishes authentication success and failure events

To understand how Spring Boot coordinates with Spring Security to achieve these behaviors, take a
look at `org.springframework.boot.security.autoconfigure.servlet` package in the Spring Boot
source code.

`SpringBootWebSecurityConfiguration` adds `@EnableWebSecurity` annotation which imports `org.springframework.security.config.annotation.web.configuration.HttpSecurityConfiguration`. `HttpSecurityConfiguration` publishes `HttpSecurity` `@Bean`:

```java
@Bean(HTTPSECURITY_BEAN_NAME)
@Scope("prototype")
HttpSecurity httpSecurity() throws Exception {
    RequestMatcherFactory.setApplicationContext(this.context);
    LazyPasswordEncoder passwordEncoder = new LazyPasswordEncoder(this.context);
    AuthenticationManagerBuilder authenticationBuilder = new DefaultPasswordEncoderAuthenticationManagerBuilder(
            this.objectPostProcessor, passwordEncoder);
    authenticationBuilder.parentAuthenticationManager(authenticationManager());
    authenticationBuilder.authenticationEventPublisher(getAuthenticationEventPublisher());
    HttpSecurity http = new HttpSecurity(this.objectPostProcessor, authenticationBuilder, createSharedObjects());
    WebAsyncManagerIntegrationFilter webAsyncManagerIntegrationFilter = new WebAsyncManagerIntegrationFilter();
    webAsyncManagerIntegrationFilter.setSecurityContextHolderStrategy(this.securityContextHolderStrategy);
    // @formatter:off
    http
        .csrf(withDefaults())
        .addFilter(webAsyncManagerIntegrationFilter)
        .exceptionHandling(withDefaults())
        .headers(withDefaults())
        .sessionManagement(withDefaults())
        .securityContext(withDefaults())
        .requestCache(withDefaults())
        .anonymous(withDefaults())
        .servletApi(withDefaults())
        .apply(new DefaultLoginPageConfigurer<>());
    http.logout(withDefaults());
    // @formatter:on
    applyCorsIfAvailable(http);
    applyDefaultConfigurers(http);
    return http;
}
```


`SpringBootWebSecurityConfiguration` also publishes a `SecurityFilterChain` `@Bean` that applies
the following configuration:

```java
@Bean
@Order(SecurityProperties.BASIC_AUTH_ORDER)
SecurityFilterChain defaultSecurityFilterChain(HttpSecurity http) throws Exception {
  http.authorizeHttpRequests((requests) -> requests.anyRequest().authenticated());
  http.formLogin(withDefaults());
  http.httpBasic(withDefaults());
  return http.build();
}
```

`UserDetailsServiceAutoConnfiguration` class publishes an `UserDetailsService` `@Bean` with a username of `user` and a randomly generated password that is logged to the console:

```java
@Bean
@ConditionalOnMissingBean(UserDetailsService.class)
public InMemoryUserDetailsManager inMemoryUserDetailsManager(SecurityProperties properties,
    ObjectProvider<PasswordEncoder> passwordEncoder) {
  SecurityProperties.User user = properties.getUser();
  List<String> roles = user.getRoles();
  return new InMemoryUserDetailsManager(User.withUsername(user.getName())
      .password(getOrDeducePassword(user, passwordEncoder.getIfAvailable()))
      .roles(StringUtils.toStringArray(roles))
      .build());
}
```

`SecurityAutoConfiguration` class publishes an `AuthenticationEventPublisher` `@Bean` for publishing authentication events:

```java

@Bean
@ConditionalOnMissingBean(AuthenticationEventPublisher.class)
public DefaultAuthenticationEventPublisher authenticationEventPublisher(
    ApplicationEventPublisher publisher) {
  return new DefaultAuthenticationEventPublisher(publisher);
}
```
