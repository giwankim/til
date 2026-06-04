# Core Spring Resilience Features: @ConcurrencyLimit, @Retryable, and RetryTemplate

Spring Framework 7.0 includes new *resilience* features: **concurrency throttling** and **retry support**.

## Concurrency Throttling

Concurrency throttling effectively protects the target resource from being accessed from too many threads at the same time.

For asynchronous tasks, this can be constrained via the `concurrencyLimit` property on `SimpleAsyncTaskExecutor`. For synchronous invocations, this can be constrained via the `concurrencyLimit` property on `ConcurrencyThrottleInterceptor` which has existed since Spring Framework 1.0 for programmatic use with the AOP framework.

Asynchronous task:

```kotlin
@Configuration
class TaskConfig {
    @Bean
    fun taskExecutor(): SimpleAsyncTaskExecutor {
        return SimpleAsyncTaskExecutor("custom-executor-").apply {
            // Limit to 10 concurrent threads
            concurrencyLimit = 10

            // Optional: Enable throttling mechanism (default is true when limit > -1)
            // setThrottleActive(true)
        }
    }
}

@Service
class EmailService(private val taskExecutor: AsyncTaskExecutor) {
    fun sendEmail(recipient: String) {
        taskExecutor.submit {
            // If 10 emails are already sending, this call BLOCKS here
            // until one finishes.
            println("Serving $recipient on ${Thread.currentThread().name}")
        }
    }
}
```

Synchronous example:

```kotlin
@Configuration
class ResilienceConfig {
    @Bean
    fun concurrencyThrottleInterceptor(): ConcurrencyThrottleInterceptor {
        val interceptor = ConcurrencyThrottleInterceptor()
        // Limit to 5 concurrent executions
        interceptor.concurrencyLimit = 5
        return interceptor
    }
}

@Configuration
class AppConfig {
    @Bean
    fun myServiceTarget(): MyService {
        return MyServiceImpl()
    }

    @Bean
    fun myService(interceptor: ConcurrencyThrottleInterceptor): ProxyFactoryBean {
        return ProxyFactoryBean().apply {
            targetName = "myServiceTarget"
            setInterceptorNames("concurrencyThrottleInterceptor")
        }
    }
}
```

With Spring Framework 7.0, configuring a concurrency limit for a given method invocation has become much easier.

- Annotate a method in Spring-managed component with `@ConcurrencyLimit` and annotate a `@Configuration` class with `@EnableResilientMethods` to enable automatic throttling.
- `@ConcurrencyLimit` can be declared at the type level to have it applied to all proxy-invoked methods in a given class hierarchy
- `@ConcurrencyLimit` can be explicitly enabled by defining a `ConcurrencyLimitBeanPostProcessor` bean in the context.

```kotlin
@ConcurrencyLimit(10)
fun sendNotification {
    this.jmsClient.destination("notification").send(...)
}
```

## Retry Support

### Declarative Retry with `@Retryable`

### Programmatic Retry with `RetryTemplate`
