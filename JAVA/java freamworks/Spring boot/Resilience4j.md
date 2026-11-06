# Resilience4j Tutorial: Basic to Advanced
[[Spring Boot]]

Resilience4j is a lightweight fault-tolerance library for Java and Spring Boot. It helps your application continue working when another service is slow, unavailable, overloaded, or returning errors.

For example, imagine you have a Spring Boot microservices application:

API Gateway

Order Service

Resilience4j

Circuit Breaker · Retry · Time Limiter · Bulkhead · Rate Limiter

Payment Service

If the payment service fails, Resilience4j helps control the failure instead of letting it spread uncontrollably.

We'll learn with practical Java and Spring Boot examples, progressing from basic annotations to production-style microservice resilience.

## 1. What problems does Resilience4j solve?

Suppose your Order Service calls a Payment Service.

Without resilience mechanisms:

- The payment service becomes slow.
    
- Order requests keep waiting.
    
- Threads and connections become occupied.
    
- Requests begin timing out.
    
- Other parts of your application may also become unavailable.
    

Resilience4j provides several independent mechanisms to reduce these problems.

|Feature|Purpose|Example|
|---|---|---|
|Retry|Repeat a failed operation|Retry a temporary network error|
|Circuit Breaker|Stop calling an unhealthy service|Temporarily reject payment calls|
|Time Limiter|Limit how long an operation can take|Cancel a future after 2 seconds|
|Bulkhead|Limit concurrent operations|Allow only 10 concurrent payment calls|
|Rate Limiter|Limit calls per time period|Allow 20 calls per second|
|Fallback|Return an alternative result|Show cached data if a service fails|

Important: These mechanisms solve different problems. A circuit breaker does not automatically retry requests, and a time limiter does not necessarily stop the underlying HTTP request unless cancellation is supported by the execution mechanism.

## 2. Set up a Spring Boot project

We'll use Maven, Java, and Spring Boot.

For a new project, use a compatible Spring Boot 3.x or 4.x setup with the matching Resilience4j release. The examples below use the familiar Spring Boot 3 programming model; check the compatibility matrix before selecting exact versions.

Add the Spring Web, Actuator, and Resilience4j Spring Boot starter dependencies.

### Maven dependencies

In `pom.xml`, add these dependencies inside `<dependencies>`:

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>

<dependency>
    <groupId>io.github.resilience4j</groupId>
    <artifactId>resilience4j-spring-boot3</artifactId>
    <version>2.3.0</version>
</dependency>
```

Resilience4j 2.x requires Java 17 or later. The Spring Boot 3 starter uses Actuator and Spring AOP for its annotation-based integration. See the [official getting-started guide](https://resilience4j.readme.io/docs/getting-started-3?utm_source=chatgpt.com).

![](https://www.google.com/s2/favicons?domain=https://resilience4j.readme.io&sz=32)

resilience4j

+1

For this tutorial, we'll assume your project already has a compatible Spring Boot parent and Java version configured.

## 3. Build a service that can fail

Create a service that simulates calling a remote payment service.

`PaymentService.java`

```
package com.example.demo.service;

import org.springframework.stereotype.Service;

@Service
public class PaymentService {

    public String processPayment() {
        // Simulate a failed external service
        throw new RuntimeException(
            "Payment service is unavailable"
        );
    }
}
```

Create a controller to expose the service:

`PaymentController.java`

```
package com.example.demo.controller;

import com.example.demo.service.PaymentService;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/payments")
public class PaymentController {

    private final PaymentService paymentService;

    public PaymentController(PaymentService paymentService) {
        this.paymentService = paymentService;
    }

    @GetMapping
    public String pay() {
        return paymentService.processPayment();
    }
}
```

Run the application and request:

```
curl -i http://localhost:8080/payments
```

The request fails because the service throws an exception. We'll add resilience mechanisms to control what happens.

## 4. Retry — automatically retry failed calls

Retry is useful when failures are temporary, such as a brief network interruption.

Example: 3 total attempts

Request 1

Request 2

Request 3

Two retries after the initial attempt, assuming the first two attempts fail and the third succeeds.

### Step 1: Add the annotation

Update the service:

```
import io.github.resilience4j.retry.annotation.Retry;

@Service
public class PaymentService {

    @Retry(name = "paymentService")
    public String processPayment() {
        System.out.println("Calling payment service");

        throw new RuntimeException("Payment failed");
    }
}
```

### Step 2: Configure Retry

Add this to `src/main/resources/application.yml`:

```
resilience4j:
  retry:
    instances:
      paymentService:
        maxAttempts: 3
        waitDuration: 1s
```

Now the method can be attempted up to three times, with a one-second wait between retry attempts.

Remember: `maxAttempts: 3` means three attempts in total, not three retries plus the original request.

### Step 3: Test it

```
curl -i http://localhost:8080/payments
```

Check your application logs. You should see the payment call attempted three times, followed by the exception being propagated because we have not configured a fallback yet.

### Advanced Retry configuration

```
resilience4j:
  retry:
    instances:
      paymentService:
        maxAttempts: 3
        waitDuration: 500ms
        enableExponentialBackoff: true
        exponentialBackoffMultiplier: 2
        retryExceptions:
          - java.io.IOException
```

This example retries `IOException` failures, with increasing wait intervals. In production, select exception types that represent genuinely temporary failures.

Do not blindly retry payment creation or other non-idempotent operations: a payment may have succeeded even if the response was lost. Use idempotency keys or equivalent deduplication to prevent duplicate charges.

## 5. Fallback — return an alternative result

A fallback is a recovery method that runs when the protected operation fails and the configured resilience mechanism propagates the failure to recovery handling.

Update `PaymentService.java`:

```
import io.github.resilience4j.retry.annotation.Retry;

@Service
public class PaymentService {

    @Retry(
        name = "paymentService",
        fallbackMethod = "paymentFallback"
    )
    public String processPayment() {
        throw new RuntimeException("Payment failed");
    }

    public String paymentFallback(Exception ex) {
        return "Payment is temporarily unavailable";
    }
}
```

The fallback method must be in the same class for this annotation-based example. Its parameters should match the original method's parameters, followed by one exception parameter.

Test again:

```
curl http://localhost:8080/payments
```

Expected response:

```
Payment is temporarily unavailable
```

The fallback does not mean the payment succeeded. For real payment processing, return a response that clearly indicates the payment is pending, failed, or unavailable rather than falsely confirming success.

## 6. Circuit Breaker — stop calling an unhealthy service

This is one of the most important Resilience4j features for microservices.

A circuit breaker has three main states:

CLOSED

Requests are allowed. Failures are monitored.

Failure rate reaches the configured threshold

OPEN

Requests are rejected immediately without calling the remote service.

HALF_OPEN

A limited number of test calls are allowed to check whether the service has recovered.

If the test calls succeed, the breaker closes. If enough fail, it opens again.

### Step 1: Add the annotation

```
import io.github.resilience4j.circuitbreaker.annotation.CircuitBreaker;

@Service
public class PaymentService {

    @CircuitBreaker(
        name = "paymentService",
        fallbackMethod = "paymentFallback"
    )
    public String processPayment() {
        System.out.println("Calling payment service");

        throw new RuntimeException("Payment service down");
    }

    public String paymentFallback(Exception ex) {
        return "Payment service unavailable";
    }
}
```

### Step 2: Configure the circuit breaker

Add to `application.yml`:

```
resilience4j:
  circuitbreaker:
    instances:
      paymentService:
        slidingWindowType: COUNT_BASED
        slidingWindowSize: 10
        minimumNumberOfCalls: 5
        failureRateThreshold: 50
        waitDurationInOpenState: 10s
        permittedNumberOfCallsInHalfOpenState: 3
```

What these properties mean:

|Property|Meaning|
|---|---|
|`slidingWindowSize: 10`|Evaluate a window of 10 calls|
|`minimumNumberOfCalls: 5`|Collect at least 5 calls before evaluating the failure rate|
|`failureRateThreshold: 50`|Open when the measured failure rate reaches 50% or more|
|`waitDurationInOpenState: 10s`|Wait 10 seconds before moving toward half-open|
|`permittedNumberOfCallsInHalfOpenState: 3`|Allow 3 trial calls in half-open state|

With these settings, after at least five recorded calls, a failure rate of 50% or higher can open the breaker. The window and minimum-call settings are important: a single failure should not necessarily cause the circuit to open.

### Step 3: Test it

Start the application and call the endpoint repeatedly:

```
for i in {1..12}; do
  curl -s http://localhost:8080/payments
  echo
done
```

Once the circuit opens, subsequent calls can fail immediately with a `CallNotPermittedException`. If you have configured the fallback, that rejection can also be handled by the fallback.

Why is this useful?

Without a circuit breaker, every incoming order might continue waiting on a payment service that is already failing. The breaker prevents unnecessary calls and helps preserve resources in the calling service.

Read more in the [official CircuitBreaker guide](https://resilience4j.readme.io/docs/circuitbreaker?utm_source=chatgpt.com).

![](https://www.google.com/s2/favicons?domain=https://resilience4j.readme.io&sz=32)

resilience4j

+1

## 7. Rate Limiter — control the number of calls

A rate limiter controls how many operations can be permitted during a time period.

For example, allow a service to make 5 calls per second to a third-party API.

```
import io.github.resilience4j.ratelimiter.annotation.RateLimiter;

@RateLimiter(
    name = "paymentService",
    fallbackMethod = "paymentFallback"
)
public String processPayment() {
    return "Payment request accepted";
}
```

Configuration:

```
resilience4j:
  ratelimiter:
    instances:
      paymentService:
        limitForPeriod: 5
        limitRefreshPeriod: 1s
        timeoutDuration: 0
```

Here:

- `limitForPeriod: 5` permits five calls per refresh period.
    
- `limitRefreshPeriod: 1s` refreshes permissions every second.
    
- `timeoutDuration: 0` means a call that cannot obtain permission immediately does not wait for permission.
    

When the limit is exceeded, Resilience4j rejects the call with a `RequestNotPermitted` exception, which you can handle using a compatible fallback.

### Rate Limiter vs API Gateway rate limiting

- Resilience4j RateLimiter: commonly controls calls made by an individual application instance or service.
    
- API Gateway rate limiting: commonly protects incoming API traffic.
    
- Distributed rate limiting: requires coordination or shared state if you need one global limit across multiple service instances.
    

If you deploy five service replicas, do not assume a local limit of five calls per second automatically means a global limit of five calls per second.

## 8. Bulkhead — limit concurrent calls

A bulkhead prevents one slow dependency from consuming all the capacity of your application.

Imagine your service can safely handle only ten concurrent payment calls. A semaphore bulkhead can limit that concurrency.

```
import io.github.resilience4j.bulkhead.annotation.Bulkhead;

@Bulkhead(
    name = "paymentService",
    fallbackMethod = "paymentFallback"
)
public String processPayment() throws InterruptedException {
    Thread.sleep(3000);
    return "Payment completed";
}
```

Configuration:

```
resilience4j:
  bulkhead:
    instances:
      paymentService:
        maxConcurrentCalls: 10
        maxWaitDuration: 0
```

If ten calls are already occupying the bulkhead, additional calls are rejected immediately rather than waiting for a slot.

### Two types of bulkhead

|Type|How it works|
|---|---|
|Semaphore bulkhead|Limits concurrent executions without creating a separate thread pool|
|Thread-pool bulkhead|Runs supported asynchronous work using a dedicated thread pool and queue|

For example, isolating payment and inventory calls into separate concurrency limits helps stop a slow payment provider from consuming all the capacity reserved for inventory work.

The default annotation bulkhead is semaphore-based. Use `Bulkhead.Type.THREADPOOL` when you specifically need the thread-pool implementation and your method uses a supported asynchronous return type.

![](https://www.google.com/s2/favicons?domain=https://resilience4j.readme.io&sz=32)

resilience4j

+1

## 9. Time Limiter — prevent indefinite waiting

A TimeLimiter controls how long an asynchronous operation is allowed to wait for completion.

It is most useful with `CompletionStage` or `CompletableFuture`-based operations. It is not a universal mechanism for interrupting every synchronous HTTP call.

Example:

```
import io.github.resilience4j.timelimiter.annotation.TimeLimiter;
import java.util.concurrent.CompletableFuture;

@TimeLimiter(
    name = "paymentService",
    fallbackMethod = "paymentFallback"
)
public CompletableFuture<String> processPayment() {
    return CompletableFuture.supplyAsync(() -> {
        try {
            Thread.sleep(5000);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
            throw new RuntimeException(e);
        }

        return "Payment completed";
    });
}

public CompletableFuture<String> paymentFallback(Exception ex) {
    return CompletableFuture.completedFuture(
        "Payment request timed out or failed"
    );
}
```

Configuration:

```
resilience4j:
  timelimiter:
    instances:
      paymentService:
        timeoutDuration: 2s
        cancelRunningFuture: true
```

In this example, the TimeLimiter can time out the operation after two seconds. Whether the underlying work actually stops depends on cancellation support and the executor or client implementation.

Production tip: Set appropriate connection and response timeouts in your HTTP client as well. A Resilience4j TimeLimiter alone is not a substitute for correctly configured network timeouts.

## 10. Combine Retry and Circuit Breaker

Real applications usually need multiple mechanisms together.

For example:

- Retry handles temporary failures.
    
- Circuit Breaker stops repeated calls when failures become frequent.
    
- Fallback gives the caller a controlled response.
    

You can use multiple annotations on a service method:

```
@Retry(name = "paymentService")
@CircuitBreaker(
    name = "paymentService",
    fallbackMethod = "paymentFallback"
)
public String processPayment() {
    return paymentClient.createPayment();
}
```

Here `paymentClient.createPayment()` represents your actual HTTP client call.

Keep in mind that annotation order matters. Resilience4j's Spring aspects have a defined default order, and combining annotations can result in different retry and circuit-breaker behavior than you expect. If the exact sequence is important, use the functional API to make the execution order explicit.

![](https://www.google.com/s2/favicons?domain=https://resilience4j.readme.io&sz=32)

resilience4j

+1

### A more explicit advanced example

Using the core functional APIs, you can make the intended nesting visible:

```
import io.github.resilience4j.circuitbreaker.CircuitBreaker;
import io.github.resilience4j.retry.Retry;
import io.github.resilience4j.decorators.Decorators;

import java.util.function.Supplier;

public String callPaymentService() {
    CircuitBreaker circuitBreaker =
        circuitBreakerRegistry.circuitBreaker("paymentService");

    Retry retry =
        retryRegistry.retry("paymentService");

    Supplier<String> operation = () ->
        paymentClient.createPayment();

    Supplier<String> protectedOperation =
        Decorators.ofSupplier(operation)
            .withCircuitBreaker(circuitBreaker)
            .withRetry(retry)
            .decorate();

    return protectedOperation.get();
}
```

This illustrates explicit composition, but the imports for `Decorators` and the registry fields must match the Resilience4j modules and dependency setup in your project. The example assumes you have configured `circuitBreakerRegistry`, `retryRegistry`, and `paymentClient`.

With this composition, Retry is inside the Circuit Breaker decorator: the circuit breaker observes the overall decorated retry operation, rather than independently wrapping each inner attempt. Choose your composition intentionally.

## 11. Complete `application.yml` configuration

Here is a combined configuration you can adapt for the payment service:

```
server:
  port: 8080

management:
  endpoints:
    web:
      exposure:
        include: health,info,metrics,circuitbreakers
  endpoint:
    health:
      show-details: when_authorized

resilience4j:
  retry:
    instances:
      paymentService:
        maxAttempts: 3
        waitDuration: 500ms
        enableExponentialBackoff: true
        exponentialBackoffMultiplier: 2

  circuitbreaker:
    instances:
      paymentService:
        slidingWindowType: COUNT_BASED
        slidingWindowSize: 10
        minimumNumberOfCalls: 5
        failureRateThreshold: 50
        waitDurationInOpenState: 10s
        permittedNumberOfCallsInHalfOpenState: 3

  ratelimiter:
    instances:
      paymentService:
        limitForPeriod: 5
        limitRefreshPeriod: 1s
        timeoutDuration: 0

  bulkhead:
    instances:
      paymentService:
        maxConcurrentCalls: 10
        maxWaitDuration: 0

  timelimiter:
    instances:
      paymentService:
        timeoutDuration: 2s
        cancelRunningFuture: true
```

This configuration defines independent policies; it does not mean all five policies are automatically applied to every method. Add the relevant annotations or compose the corresponding decorators, and verify that the timeout settings match your actual HTTP client's behavior.

## 12. Monitor Resilience4j with Actuator

Resilience mechanisms are much more useful when you can see whether they are working.

The starter integrates with Spring Boot Actuator and publishes metrics. The exact endpoints available depend on your configuration and the Resilience4j version.

![](https://www.google.com/s2/favicons?domain=https://resilience4j.readme.io&sz=32)

resilience4j

+1

For example, with the endpoint exposure configured above:

```
curl http://localhost:8080/actuator/health
```

Inspect available metrics:

```
curl http://localhost:8080/actuator/metrics
```

Check circuit breaker metrics:

```
curl http://localhost:8080/actuator/metrics/resilience4j.circuitbreaker.calls
```

Inspect circuit breaker state:

```
curl http://localhost:8080/actuator/circuitbreakers
```

Useful signals include:

- Circuit breaker state: closed, open, or half-open.
    
- Number of successful, failed, and rejected calls.
    
- Retry attempts.
    
- Rate limiter permissions and rejected requests.
    
- Bulkhead available capacity and rejected calls.
    

Avoid exposing Actuator endpoints publicly without proper security. Metrics endpoints may reveal operational details about your application.

## 13. Advanced topics you should learn next

A. Spring Cloud OpenFeign integration

Protect calls between Spring Boot microservices. Learn how to configure Resilience4j with OpenFeign, choose retry policies, and return controlled error responses.

B. Events and custom monitoring

Listen for circuit breaker state transitions and retry events. Export Micrometer metrics to Prometheus and build Grafana dashboards.

C. Spring WebFlux and reactive APIs

Use `Mono` and `Flux` with the Resilience4j Reactor module. Prefer reactive operators and avoid blocking operations on reactive event-loop threads.

D. Integration testing and failure simulation

Simulate slow services, connection errors, HTTP 500 responses, and recovery. Verify that retries, fallbacks, and circuit breaker transitions behave as expected.

## 14. Production best practices

1. Retry only transient failures. Do not retry validation errors, authentication failures, or every HTTP 4xx response indiscriminately.
    
2. Set a total time budget. Three attempts with long waits can make an API request exceed its intended latency budget.
    
3. Use circuit breakers per dependency. A failing inventory service should not automatically open the circuit for payments.
    
4. Avoid retry storms. Use exponential backoff and, where appropriate, jitter.
    
5. Design meaningful fallbacks. Cached product information may be acceptable; pretending that a payment succeeded is not.
    
6. Do not confuse resilience with load balancing. Resilience4j controls failures and resource usage; it does not discover healthy service instances or distribute traffic among them.
    
7. Test recovery, not just failure. Verify that an open circuit eventually probes the service and returns to closed state when the dependency recovers.
    

## 15. Practice project: resilient microservices

Build a small Spring Boot project with an Order Service calling separate Payment and Inventory services.

## Project checklist

0 of 7

Create Order, Payment, and Inventory endpoints

Start with ordinary REST calls and test each service.

Add Retry to temporary payment failures

Verify the total attempt count and retry delay.

Add Circuit Breaker to payment calls

Force failures and inspect the breaker state.

Add fallbacks with meaningful HTTP responses

Distinguish temporary unavailability from successful processing.

Add Bulkhead and RateLimiter

Simulate concurrent traffic and excess requests.

Add client timeouts and async TimeLimiter

Check how cancellation affects actual work.

Expose Actuator metrics and write integration tests

Test failure, open-circuit rejection, and recovery.

## Official learning resources

- [Resilience4j documentation](https://resilience4j.readme.io/docs?utm_source=chatgpt.com)
    
- [Spring Boot 3 setup and annotations](https://resilience4j.readme.io/docs/getting-started-3?utm_source=chatgpt.com)
    
- [Circuit Breaker documentation](https://resilience4j.readme.io/docs/circuitbreaker?utm_source=chatgpt.com)
    
- [Resilience4j GitHub repository and examples](https://github.com/resilience4j/resilience4j?utm_source=chatgpt.com)
    

Recommended learning order: Retry → Fallback → Circuit Breaker → RateLimiter → Bulkhead → TimeLimiter → OpenFeign integration → Metrics and testing.

Once you understand these patterns individually, combining them safely is the key to building resilient Spring Boot microservices.