# Complete Spring Web Tutorial: Basic to Advanced

This tutorial covers Spring Web with Spring Boot, from your first REST API to production-ready APIs, including validation, exception handling, file uploads, pagination, security integration, asynchronous requests, testing, and performance.

We'll use Java + Spring Boot + Spring Web + Maven, with examples you can adapt to your existing Java and PostgreSQL projects.

## 1. What is Spring Web?

Spring Web is the Spring Framework module for building web applications. In Spring Boot, the `spring-boot-starter-web` starter provides the common components needed to create HTTP APIs and traditional web applications.

![REST APIs — Beginner Friendly Guide | by Swastik Sharma | Medium](https://images.openai.com/static-rsc-4/0N8vFfZhJwq3eFPXiJ8d_gIP83hhtgaFFEW2AzNXINWE7kEBJmExA4Q41RyAqOK3p0EwMocEr23EkgSdJEZgG48q-xmnKbGDaN-kD4Qyxs-JPpLYKYZrVpP01YYi8V7hkLVXXhvYAlm9_nrR6LVTUYLgvdRx95UoGZgF8hxQxl0?purpose=inline)

Spring MVC

Handles HTTP requests using controllers, request mappings, argument resolution, validation, and response conversion.

![Web MVC Filter Test on Spring Boot - Junhyunny’s Devlogs](https://images.openai.com/static-rsc-4/pYA2pYGM6OcgKClslcoI8n1AHuunr9vY-_paG4yf1OiejTR6Umt_7seNuDJh0Hsje54hGDjL6kTv97MrrEm4HO6x5-D2fbZc0Wytwr_VJfDv2bCHlxxUA0m2dMZnnqsPmxDNdLt7_MqKeZbLm_PW6uwYTy_Mpu1i4qt6AlxXz78?purpose=inline)

Embedded server

Usually Tomcat for a standard Spring Boot Web application. It receives HTTP requests and passes them into the application.

![What are HTTP requests and Spring MVC? | by Vicky’s Notes | Javarevisited | Medium](https://images.openai.com/static-rsc-4/kcblKFoF9kzKjPtWfpyQParK_T5eE5ube9irylLW1pz-B9qN29TuIlg7AQr-Kw5fW8HmZxFk-fFlu--Vj1x-H_ezQ23rVwJ_4_XQwMof6tPyU8h85ekGf8HdUneEggim1hDl1gCXkOnrPnVFUlUkKkw3seikWSnHxVYXpuaWN78?purpose=inline)

DispatcherServlet

The central Spring MVC component that routes incoming requests to the appropriate controller.

### Spring Web vs Spring WebFlux

|Spring MVC|Spring WebFlux|
|---|---|
|Traditional servlet-based web stack|Reactive web stack|
|Commonly uses blocking JDBC/JPA|Can use non-blocking database clients|
|Great for typical CRUD APIs|Useful for reactive, streaming, or high-concurrency workloads|
|`spring-boot-starter-web`|`spring-boot-starter-webflux`|

Important: Spring Web and Spring Data JPA solve different problems. Spring Web handles HTTP; JPA handles database persistence.

## 2. Create your first project

Use Spring Initializr: [start.spring.io](https://start.spring.io/?utm_source=chatgpt.com).

Choose:

- Project: Maven
    
- Language: Java
    
- Java: 17 or later, compatible with your selected Spring Boot version
    
- Dependencies: Spring Web
    

Your main Maven dependency is:

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

A typical project structure:

```
src/main/java/com/example/demo/
├── DemoApplication.java
├── controller/
│   └── HelloController.java
├── service/
│   └── HelloService.java
├── dto/
└── exception/
```

Run it on Fedora or another Linux system:

```
./mvnw spring-boot:run
```

The default HTTP port is `8080`, unless you configure another port.

## 3. Understand the request lifecycle

Client

Browser, React, Postman, mobile app

Embedded Tomcat

Receives the HTTP request

DispatcherServlet

Finds the matching request handler

Controller → Service → Repository

Handle the request and business logic

HTTP response

Status code, headers and JSON body

Spring MVC's `DispatcherServlet` coordinates request processing, including handler selection and response conversion.

![](https://www.google.com/s2/favicons?domain=https://springframework.org&sz=32)

Home

For example, a React application sends:

```
GET /api/products/10 HTTP/1.1
Host: localhost:8080
Accept: application/json
```

Spring finds the controller method mapped to `/api/products/10`, executes the required logic and returns a JSON response.

```
{
  "id": 10,
  "name": "Keyboard",
  "price": 999.0
}
```

The JSON conversion is normally handled automatically by Spring's HTTP message converters and Jackson.

## 4. Your first controller

Create `HelloController.java`:

```
package com.example.demo.controller;

import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api/hello")
public class HelloController {

    @GetMapping
    public String hello() {
        return "Hello Spring Web";
    }

    @GetMapping("/user")
    public String greet(
            @RequestParam(defaultValue = "Guest") String name) {
        return "Hello " + name;
    }

    @GetMapping("/{id}")
    public String getUser(@PathVariable long id) {
        return "User ID: " + id;
    }
}
```

Test the endpoints:

```
curl http://localhost:8080/api/hello
curl "http://localhost:8080/api/hello/user?name=Alex"
curl http://localhost:8080/api/hello/25
```

### Important annotations

|Annotation|Purpose|
|---|---|
|`@RestController`|Marks a controller whose return values normally become response bodies|
|`@Controller`|Used primarily for MVC controllers and view rendering|
|`@RequestMapping`|Maps requests by path and other conditions|
|`@GetMapping`|Handles GET requests|
|`@PostMapping`|Handles POST requests|
|`@PutMapping`|Handles PUT requests|
|`@PatchMapping`|Handles PATCH requests|
|`@DeleteMapping`|Handles DELETE requests|
|`@PathVariable`|Reads a value from the URL path|
|`@RequestParam`|Reads a query parameter or form parameter|
|`@RequestBody`|Converts the request body into a Java object|
|`@ResponseBody`|Writes a method's return value to the response body|

Difference between `@Controller` and `@RestController`: `@RestController` combines `@Controller` with `@ResponseBody`. With a regular `@Controller`, a returned string is often interpreted as a view name unless response-body handling is specified.

## 5. HTTP methods and REST API design

A REST API exposes resources using predictable URLs and HTTP methods.

Suppose your resource is `products`.

|Method|Endpoint|Purpose|Typical success status|
|---|---|---|---|
|GET|`/api/products`|List products|`200 OK`|
|GET|`/api/products/10`|Get one product|`200 OK`|
|POST|`/api/products`|Create a product|`201 Created`|
|PUT|`/api/products/10`|Replace a product|`200 OK` or `204 No Content`|
|PATCH|`/api/products/10`|Partially update a product|`200 OK`|
|DELETE|`/api/products/10`|Delete a product|`204 No Content`|

Use nouns for resources, such as `/products`, rather than action-based paths such as `/getAllProducts`.

A request to create a product could be:

```
POST /api/products
Content-Type: application/json
```

```
{
  "name": "Mechanical Keyboard",
  "price": 2499.00
}
```

The server validates the input, saves the product and returns its ID.

### PUT vs PATCH

- PUT: generally replaces the resource representation.
    
- PATCH: applies a partial update.
    
- POST: commonly creates a resource or performs an operation.
    
- DELETE: removes a resource.
    

GET requests should not be used to perform state-changing operations.

## 6. Build a complete CRUD REST API

We'll build a small product API. First, use an in-memory list so you can learn Spring Web without introducing database configuration yet. Later, you can replace the storage with Spring Data JPA.

### Step 1: Create a model

`Product.java`

```
package com.example.demo.model;

public record Product(
        Long id,
        String name,
        double price
) {}
```

A Java record is convenient for immutable data. Here, `id` is the server-generated identifier.

### Step 2: Create a service

`ProductService.java`

```
package com.example.demo.service;

import com.example.demo.model.Product;
import org.springframework.stereotype.Service;

import java.util.*;
import java.util.concurrent.atomic.AtomicLong;

@Service
public class ProductService {

    private final Map<Long, Product> products =
            new LinkedHashMap<>();

    private final AtomicLong sequence =
            new AtomicLong();

    public List<Product> findAll() {
        return new ArrayList<>(products.values());
    }

    public Product findById(long id) {
        Product product = products.get(id);

        if (product == null) {
            throw new NoSuchElementException(
                    "Product not found: " + id);
        }

        return product;
    }

    public Product create(Product input) {
        long id = sequence.incrementAndGet();

        Product product = new Product(
                id, input.name(), input.price());

        products.put(id, product);
        return product;
    }

    public Product update(long id, Product input) {
        findById(id);

        Product updated = new Product(
                id, input.name(), input.price());

        products.put(id, updated);
        return updated;
    }

    public void delete(long id) {
        if (products.remove(id) == null) {
            throw new NoSuchElementException(
                    "Product not found: " + id);
        }
    }
}
```

The `Map` acts as temporary storage. This is only a learning implementation: its data disappears on restart, and it is not suitable for concurrent production access.

### Step 3: Create the controller

`ProductController.java`

```
package com.example.demo.controller;

import com.example.demo.model.Product;
import com.example.demo.service.ProductService;
import org.springframework.http.*;
import org.springframework.web.bind.annotation.*;

import java.net.URI;
import java.util.List;

@RestController
@RequestMapping("/api/products")
public class ProductController {

    private final ProductService service;

    public ProductController(ProductService service) {
        this.service = service;
    }

    @GetMapping
    public List<Product> getAll() {
        return service.findAll();
    }

    @GetMapping("/{id}")
    public Product getOne(@PathVariable long id) {
        return service.findById(id);
    }

    @PostMapping
    public ResponseEntity<Product> create(
            @RequestBody Product product) {

        Product saved = service.create(product);

        return ResponseEntity
                .created(URI.create("/api/products/" + saved.id()))
                .body(saved);
    }

    @PutMapping("/{id}")
    public Product update(
            @PathVariable long id,
            @RequestBody Product product) {
        return service.update(id, product);
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> delete(
            @PathVariable long id) {
        service.delete(id);
        return ResponseEntity.noContent().build();
    }
}
```

Notice the constructor injection. Spring supplies the `ProductService` bean automatically because it is annotated with `@Service`.

### Step 4: Test the CRUD API

Create:

```
curl -X POST http://localhost:8080/api/products \
  -H "Content-Type: application/json" \
  -d '{"name":"Mouse","price":599}'
```

Read:

```
curl http://localhost:8080/api/products
curl http://localhost:8080/api/products/1
```

Update:

```
curl -X PUT http://localhost:8080/api/products/1 \
  -H "Content-Type: application/json" \
  -d '{"name":"Wireless Mouse","price":899}'
```

Delete:

```
curl -i -X DELETE http://localhost:8080/api/products/1
```

You can run these commands from your Linux terminal. Postman works too.

## 7. Request bodies, query parameters and headers

These are three different ways to send data to a Spring controller.

### A. `@RequestBody`

Used for JSON or other supported request bodies.

```
@PostMapping("/search")
public String search(@RequestBody SearchRequest request) {
    return "Searching for " + request.keyword();
}

public record SearchRequest(String keyword) {}
```

Example JSON:

```
{
  "keyword": "Spring Boot"
}
```

### B. `@RequestParam`

Used for query parameters.

```
@GetMapping("/search")
public String search(
        @RequestParam String keyword,
        @RequestParam(defaultValue = "0") int page) {

    return keyword + ", page " + page;
}
```

Request:

```
GET /api/products/search?keyword=mouse&page=2
```

### C. `@RequestHeader`

Used to read an HTTP header.

```
@GetMapping("/client")
public String client(
        @RequestHeader("User-Agent") String userAgent) {

    return userAgent;
}
```

You can also use `HttpServletRequest` when you need lower-level servlet request information, but annotation-based binding is usually simpler for ordinary endpoints.

## 8. DTOs: separate API data from internal models

In real projects, avoid accepting your persistence entity directly as the request body. Create a Data Transfer Object (DTO) to control what clients can submit and what the API exposes.

`CreateProductRequest.java`

```
import jakarta.validation.constraints.*;
import java.math.BigDecimal;

public record CreateProductRequest(
        @NotBlank
        @Size(max = 120)
        String name,

        @NotNull
        @DecimalMin("0.01")
        @Digits(integer = 10, fraction = 2)
        BigDecimal price
) {}
```

`ProductResponse.java`

```
import java.math.BigDecimal;

public record ProductResponse(
        Long id,
        String name,
        BigDecimal price
) {}
```

Why use DTOs?

- Prevent clients from setting protected fields such as internal IDs or administrative flags.
    
- Keep API contracts separate from database design.
    
- Return only the information clients need.
    
- Make validation and API versioning easier.
    

For money, prefer `BigDecimal` to `double` to avoid binary floating-point rounding issues.

## 9. Validation with Spring Web

Validation prevents invalid input from reaching your business logic.

Add the Validation dependency if your project doesn't already include it:

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

Use `@Valid` on the request body:

```
import jakarta.validation.Valid;
import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api/products")
public class ProductController {

    @PostMapping("/validated")
    @ResponseStatus(HttpStatus.CREATED)
    public CreateProductRequest create(
            @Valid @RequestBody CreateProductRequest request) {
        return request;
    }
}
```

This simplified endpoint demonstrates validation only; it echoes the validated DTO rather than saving a product.

### Common validation annotations

|Annotation|Meaning|
|---|---|
|`@NotNull`|Value cannot be null|
|`@NotBlank`|String cannot be null, empty or whitespace|
|`@NotEmpty`|String or collection cannot be empty|
|`@Size(min=2, max=100)`|Restricts size|
|`@Min(1)` / `@Max(100)`|Restricts integer values|
|`@Positive`|Number must be greater than zero|
|`@Email`|Validates email format|
|`@Pattern(regexp="...")`|Validates against a regular expression|
|`@DecimalMin("0.01")`|Enforces a decimal lower bound|

For example, if the client sends an empty product name, Spring rejects the request before the controller method executes.

Important: validation and business rules are different. `@NotBlank` validates a string's format; a service might separately check whether a product name is already in use.

## 10. Global exception handling

Without proper handling, exceptions can produce inconsistent responses. Use `@RestControllerAdvice` to centralize API error responses.

Create `ApiError.java`:

```
import java.time.Instant;

public record ApiError(
        Instant timestamp,
        int status,
        String error,
        String message,
        String path
) {}
```

Create `GlobalExceptionHandler.java`:

```
import jakarta.servlet.http.HttpServletRequest;
import jakarta.validation.ConstraintViolationException;
import org.springframework.http.*;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.*;

import java.time.Instant;
import java.util.NoSuchElementException;

@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(NoSuchElementException.class)
    public ResponseEntity<ApiError> notFound(
            NoSuchElementException ex,
            HttpServletRequest request) {

        return error(HttpStatus.NOT_FOUND, ex.getMessage(),
                     request.getRequestURI());
    }

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ApiError> invalidBody(
            MethodArgumentNotValidException ex,
            HttpServletRequest request) {

        String message = ex.getBindingResult()
                .getFieldErrors()
                .stream()
                .map(e -> e.getField() + ": " + e.getDefaultMessage())
                .findFirst()
                .orElse("Invalid request");

        return error(HttpStatus.BAD_REQUEST, message,
                     request.getRequestURI());
    }

    @ExceptionHandler(ConstraintViolationException.class)
    public ResponseEntity<ApiError> invalidParameter(
            ConstraintViolationException ex,
            HttpServletRequest request) {

        return error(HttpStatus.BAD_REQUEST, ex.getMessage(),
                     request.getRequestURI());
    }

    private ResponseEntity<ApiError> error(
            HttpStatus status, String message, String path) {

        return ResponseEntity.status(status).body(
                new ApiError(
                        Instant.now(),
                        status.value(),
                        status.getReasonPhrase(),
                        message,
                        path
                )
        );
    }
}
```

Example error response:

```
{
  "timestamp": "2026-10-24T12:00:00Z",
  "status": 404,
  "error": "Not Found",
  "message": "Product not found: 99",
  "path": "/api/products/99"
}
```

The timestamp here is illustrative.

You can also handle `IllegalArgumentException`, database constraint violations, and unexpected exceptions. For unexpected errors, log the full exception internally but return a generic message to the client. Avoid exposing stack traces, SQL statements, or secrets.

## 11. `ResponseEntity` and HTTP status codes

`ResponseEntity<T>` lets you control the response body, status and headers.

```
@GetMapping("/{id}")
public ResponseEntity<Product> getProduct(
        @PathVariable long id) {

    Product product = service.findById(id);

    return ResponseEntity.ok(product);
}
```

Other common uses:

```
return ResponseEntity.notFound().build();
```

```
return ResponseEntity.noContent().build();
```

```
return ResponseEntity
        .status(HttpStatus.CONFLICT)
        .body(errorResponse);
```

Use the status that matches the result:

- `200 OK`: request succeeded.
    
- `201 Created`: resource created.
    
- `204 No Content`: successful operation with no response body.
    
- `400 Bad Request`: invalid request.
    
- `401 Unauthorized`: authentication is missing or invalid.
    
- `403 Forbidden`: caller lacks permission.
    
- `404 Not Found`: resource does not exist.
    
- `409 Conflict`: request conflicts with current state.
    
- `500 Internal Server Error`: unexpected server failure.
    
- `503 Service Unavailable`: service temporarily unavailable.
    

Authentication and authorization status codes should normally be handled by your security configuration, not only by controller methods.

## 12. Filters, interceptors and AOP

These mechanisms can all run code around your business endpoints, but they work at different levels.

Filter — Servlet layer

Useful for request/response processing, correlation IDs and integration with security filters.

HandlerInterceptor — Spring MVC layer

Useful for logic before and after controller handling, such as request timing or locale handling.

AOP — Spring bean method layer

Useful for cross-cutting method behavior, such as tracing, auditing or custom annotations.

### A. Create a filter

```
import jakarta.servlet.*;
import jakarta.servlet.http.HttpServletRequest;
import org.springframework.stereotype.Component;

import java.io.IOException;
import java.util.UUID;

@Component
public class RequestIdFilter implements Filter {

    @Override
    public void doFilter(
            ServletRequest request,
            ServletResponse response,
            FilterChain chain)
            throws IOException, ServletException {

        HttpServletRequest http =
                (HttpServletRequest) request;

        String requestId = UUID.randomUUID().toString();

        System.out.println(
                requestId + " " + http.getMethod()
                + " " + http.getRequestURI());

        chain.doFilter(request, response);
    }
}
```

`chain.doFilter()` is essential: it passes control to the next filter or servlet. Without it, the request may never reach your controller.

In production, use a proper logging framework and MDC/correlation-ID integration instead of `System.out.println()`.

### B. Create an interceptor

```
import jakarta.servlet.http.*;
import org.springframework.stereotype.Component;
import org.springframework.web.servlet.HandlerInterceptor;

@Component
public class TimingInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(
            HttpServletRequest request,
            HttpServletResponse response,
            Object handler) {

        request.setAttribute(
                "startTime", System.nanoTime());

        return true;
    }

    @Override
    public void afterCompletion(
            HttpServletRequest request,
            HttpServletResponse response,
            Object handler,
            Exception ex) {

        long start = (long) request.getAttribute("startTime");

        long elapsedMs =
                (System.nanoTime() - start) / 1_000_000;

        System.out.println(
                request.getRequestURI()
                + " took " + elapsedMs + " ms");
    }
}
```

Register it:

```
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.*;

@Configuration
public class WebConfig implements WebMvcConfigurer {

    private final TimingInterceptor timingInterceptor;

    public WebConfig(TimingInterceptor timingInterceptor) {
        this.timingInterceptor = timingInterceptor;
    }

    @Override
    public void addInterceptors(
            InterceptorRegistry registry) {

        registry.addInterceptor(timingInterceptor)
                .addPathPatterns("/api/**")
                .excludePathPatterns("/api/health");
    }
}
```

## 13. CORS: connect React to Spring Boot

Suppose your React application runs on `http://localhost:5173` and your Spring Boot API runs on `http://localhost:8080`.

These are different origins because their ports differ. Browsers enforce Cross-Origin Resource Sharing (CORS) rules.

For a simple project, you can configure CORS globally:

```
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.*;

@Configuration
public class CorsConfig implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/api/**")
                .allowedOrigins("http://localhost:5173")
                .allowedMethods(
                        "GET", "POST", "PUT",
                        "PATCH", "DELETE")
                .allowedHeaders("*");
    }
}
```

This allows the specified frontend origin to access matching endpoints. Spring MVC also supports `@CrossOrigin` on controllers or individual methods.

![](https://www.google.com/s2/favicons?domain=https://github.com&sz=32)

GitHub

+1

Production rules:

- Allow only trusted frontend origins.
    
- Configure CORS in coordination with Spring Security when security is enabled.
    
- Do not use wildcard origins for credentialed requests.
    
- CORS is a browser access policy, not authentication or authorization.
    

## 14. File upload and download

Spring Web supports `multipart/form-data` requests, commonly used for profile images, PDFs and documents.

### Upload endpoint

```
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.multipart.MultipartFile;

@RestController
@RequestMapping("/api/files")
public class FileController {

    @PostMapping("/upload")
    public ResponseEntity<String> upload(
            @RequestParam("file") MultipartFile file) {

        if (file.isEmpty()) {
            return ResponseEntity.badRequest()
                    .body("File is empty");
        }

        return ResponseEntity.ok(
                "Received: " + file.getOriginalFilename()
                + ", bytes: " + file.getSize());
    }
}
```

This only receives the file; it does not persist it. To save a file, copy its contents to a controlled storage location or object store.

Test the endpoint:

```
curl -X POST http://localhost:8080/api/files/upload \
  -F "file=@document.pdf"
```

### Configure upload limits

In `application.properties`:

```
spring.servlet.multipart.max-file-size=10MB
spring.servlet.multipart.max-request-size=12MB
```

For a real upload service, also:

- Generate server-side filenames rather than trusting user-supplied names.
    
- Validate file type and content, not just the extension.
    
- Store files outside public executable directories.
    
- Restrict access to private documents.
    
- Handle storage failures and cleanup.
    
- Consider malware scanning when appropriate.
    

Spring's official guide covers the complete upload workflow.

![](https://www.google.com/s2/favicons?domain=https://spring.io&sz=32)

Uploading Files

### Download endpoint

```
import org.springframework.core.io.*;
import org.springframework.http.*;
import org.springframework.web.bind.annotation.*;

import java.nio.file.*;

@GetMapping("/download/{name}")
public ResponseEntity<Resource> download(
        @PathVariable String name) throws Exception {

    // Demonstration only: use a trusted, validated storage path.
    Path path = Path.of("/srv/myapp/uploads")
            .resolve(name)
            .normalize();

    Path base = Path.of("/srv/myapp/uploads").normalize();

    if (!path.startsWith(base) || !Files.isRegularFile(path)) {
        return ResponseEntity.notFound().build();
    }

    Resource resource = new FileSystemResource(path);

    return ResponseEntity.ok()
            .contentType(MediaType.APPLICATION_OCTET_STREAM)
            .header(HttpHeaders.CONTENT_DISPOSITION,
                    "attachment; filename=\""
                    + path.getFileName() + "\"")
            .body(resource);
}
```

This example assumes the storage directory exists and is trusted. For production, also validate filenames, reject symbolic-link escapes where relevant, and use safe `Content-Disposition` encoding.

## 15. Pagination, sorting and filtering

Returning every database row is inefficient when a table contains thousands or millions of records.

A paginated endpoint might look like:

```
GET /api/products?page=0&size=20&sort=name,asc
```

For database-backed APIs, Spring Data provides pagination abstractions such as `Pageable`, `Page` and `Sort`.

```
@GetMapping
public Page<ProductResponse> getProducts(
        @PageableDefault(size = 20, sort = "name")
        Pageable pageable) {

    return productRepository.findAll(pageable)
            .map(product -> new ProductResponse(
                    product.getId(),
                    product.getName(),
                    product.getPrice()));
}
```

This example assumes `productRepository` is a Spring Data repository and the entity has the corresponding fields. Add the Spring Data JPA dependency and configure a database to use it.

A response generally includes:

- The content for the requested page.
    
- Current page and page size.
    
- Total element count and page count.
    

For a large dataset, `Slice<T>` may be preferable if you only need to know whether another page exists, without calculating the total count. For very large or frequently changing datasets, consider cursor/keyset pagination.

Never allow arbitrary user input to become an unchecked SQL fragment or unrestricted sort expression.

## 16. Serving static files and HTML pages

Spring Web can serve both REST APIs and traditional web pages.

By default, static resources can be placed in:

```
src/main/resources/static/
├── index.html
├── css/
│   └── style.css
└── js/
    └── app.js
```

For example, `static/index.html` is usually accessible at:

```
http://localhost:8080/index.html
```

For server-rendered HTML, you can use a template engine such as Thymeleaf with a `@Controller`.

```
@Controller
public class PageController {

    @GetMapping("/home")
    public String home() {
        return "home";
    }
}
```

With Thymeleaf configured, this normally resolves to `templates/home.html`.

Use:

- REST controllers for JSON APIs used by React, mobile applications or other services.
    
- MVC controllers and templates for server-rendered pages.
    
- Static resources for HTML, CSS, JavaScript, images and other assets.
    

## 17. Asynchronous processing and long-running requests

Sometimes an endpoint needs to wait for a background operation, a slow external service, or an event.

Spring MVC supports asynchronous processing with `Callable`, `DeferredResult`, `WebAsyncTask` and streaming response types.

![](https://www.google.com/s2/favicons?domain=https://www.springframework.org&sz=32)

Home

### A. `Callable`

```
@GetMapping("/report")
public Callable<String> generateReport() {
    return () -> {
        Thread.sleep(2000); // Demonstration only
        return "Report ready";
    };
}
```

Spring executes the callable using its asynchronous task executor rather than keeping the initial servlet request thread occupied for the entire operation.

However, this does not automatically make the underlying work non-blocking. Database calls and `Thread.sleep()` still block the worker thread.

### B. `DeferredResult`

Use this when another part of the application will produce the result later.

```
@GetMapping("/long-task")
public DeferredResult<String> longTask() {
    DeferredResult<String> result =
            new DeferredResult<>(10_000L);

    result.setResult("Task completed");
    return result;
}
```

This example completes immediately, so it only demonstrates the return type. In a real implementation, retain the result in a controlled task registry and complete it when the asynchronous work finishes. Add timeout handling, cancellation and cleanup.

### C. Server-Sent Events (SSE)

SSE allows a server to push multiple events to a browser over one HTTP connection.

```
@GetMapping(
        value = "/events",
        produces = MediaType.TEXT_EVENT_STREAM_VALUE)
public SseEmitter events() {
    SseEmitter emitter = new SseEmitter(60_000L);

    Thread.startVirtualThread(() -> {
        try {
            for (int i = 1; i <= 5; i++) {
                emitter.send(
                        SseEmitter.event()
                                .name("progress")
                                .data("Step " + i));

                Thread.sleep(1000);
            }

            emitter.complete();
        } catch (Exception ex) {
            emitter.completeWithError(ex);
        }
    });

    return emitter;
}
```

This uses Java 21 or later because of `Thread.startVirtualThread`. A managed executor is generally preferable for production code.

Example uses:

- AI-generated text streaming.
    
- Upload or report progress.
    
- Notifications.
    
- Live dashboards.
    

SSE is one-way: server to client. If you need bidirectional messaging, consider WebSocket.

## 18. HTTP clients: call other services

Spring Web also provides HTTP client integrations, so your application can call other REST APIs.

For modern synchronous calls, Spring's `RestClient` is a good choice. It is available in Spring Framework 6.1 and later.

```
import org.springframework.stereotype.Service;
import org.springframework.web.client.RestClient;

@Service
public class ExternalPostService {

    private final RestClient client;

    public ExternalPostService(RestClient.Builder builder) {
        this.client = builder
                .baseUrl("https://jsonplaceholder.typicode.com")
                .build();
    }

    public String getPost() {
        return client.get()
                .uri("/posts/1")
                .retrieve()
                .body(String.class);
    }
}
```

You can then inject `ExternalPostService` into a controller or another service.

### Which HTTP client should you use?

|Client|Best fit|
|---|---|
|`RestClient`|Modern synchronous HTTP calls|
|`WebClient`|Reactive, non-blocking HTTP calls and streaming|
|`RestTemplate`|Existing applications; generally not the first choice for new synchronous code|
|OpenFeign|Declarative HTTP clients, often used with Spring Cloud|

For production, configure timeouts, authentication, error handling, connection management and resilience. A slow remote service should not consume all your application's request threads.

## 19. Content negotiation and custom response headers

Spring MVC normally uses the request's `Accept` header to determine which response media type is acceptable. This is called content negotiation.

![](https://www.google.com/s2/favicons?domain=https://springframework.org&sz=32)

Home

For example:

```
Accept: application/json
```

asks for JSON. The `Content-Type` request header, by contrast, tells the server what format the request body uses.

You can explicitly set the response type:

```
@GetMapping(
        value = "/message",
        produces = MediaType.APPLICATION_JSON_VALUE)
public Map<String, String> message() {
    return Map.of("message", "Hello");
}
```

You can also set custom headers:

```
@GetMapping("/version")
public ResponseEntity<String> version() {
    return ResponseEntity.ok()
            .header("X-API-Version", "1")
            .body("API version 1");
}
```

Use custom headers only when there is a clear need. Avoid using headers as a replacement for a well-designed response body.

## 20. API versioning

Versioning helps you evolve an API without breaking existing clients.

One common approach uses a path prefix:

```
/api/v1/products
/api/v2/products
```

```
@RestController
@RequestMapping("/api/v1/products")
public class ProductV1Controller {

    @GetMapping
    public String getProducts() {
        return "Product API v1";
    }
}
```

Create a separate controller or explicitly route to a different representation for version 2.

Other approaches include version headers or media-type versioning. Choose a consistent strategy, document changes, and define how long older versions will remain supported.

Modern Spring Framework versions also offer API versioning facilities in Spring MVC configuration. Check the version you selected before relying on version-specific features.

![](https://www.google.com/s2/favicons?domain=https://github.com&sz=32)

GitHub

## 21. Caching and conditional requests

Caching can reduce database work, network traffic and response latency.

HTTP caching uses headers such as:

- `Cache-Control`
    
- `ETag`
    
- `Last-Modified`
    

Example:

```
@GetMapping("/public-info")
public ResponseEntity<Map<String, String>> publicInfo() {
    return ResponseEntity.ok()
            .cacheControl(
                    CacheControl.maxAge(
                            java.time.Duration.ofMinutes(5)))
            .body(Map.of("message", "Public information"));
}
```

This asks clients and intermediaries to cache the response for up to five minutes, subject to the applicable caching rules.

Be careful: don't publicly cache personalized or sensitive responses. Caching does not automatically invalidate stale data when your database changes.

For application-level caching, Spring's caching abstraction supports `@Cacheable`, `@CachePut` and `@CacheEvict`. Those are separate from HTTP response caching.

## 22. Spring Security integration

Spring Web maps and executes requests; Spring Security handles authentication and authorization.

For example, you might want:

- Public access to `GET /api/products`.
    
- Authentication for `POST /api/products`.
    
- An administrator role for deleting products.
    

Add the Spring Security starter and configure a `SecurityFilterChain`. For example:

```
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.http.HttpMethod;
import org.springframework.security.config.Customizer;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.web.SecurityFilterChain;

@Configuration
public class SecurityConfig {

    @Bean
    SecurityFilterChain securityFilterChain(
            HttpSecurity http) throws Exception {

        return http
                .authorizeHttpRequests(auth -> auth
                        .requestMatchers(
                                HttpMethod.GET,
                                "/api/products/**").permitAll()
                        .requestMatchers("/api/admin/**")
                                .hasRole("ADMIN")
                        .anyRequest().authenticated()
                )
                .httpBasic(Customizer.withDefaults())
                .build();
    }
}
```

This demonstrates authorization rules and HTTP Basic authentication. It is not a complete production authentication configuration.

Important details:

- HTTP Basic transmits credentials with each request; use HTTPS.
    
- Browser session-based applications normally need appropriate CSRF protection.
    
- A stateless JWT API needs token validation and carefully configured session and CSRF behavior.
    
- OAuth 2.0 login, OAuth 2.0 resource servers and authorization servers are different roles.
    
- Hiding a button in React does not secure the corresponding API endpoint.
    

You can learn security independently after becoming comfortable with the controller, service and DTO layers.

## 23. Testing Spring Web APIs

Testing verifies the API contract without requiring you to start everything manually.

Add Spring Boot's test starter if it is not already included:

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

### Test a controller with MockMvc

```
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.test.web.servlet.MockMvc;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;

@WebMvcTest(HelloController.class)
class HelloControllerTest {

    @Autowired
    MockMvc mockMvc;

    @Test
    void returnsHello() throws Exception {
        mockMvc.perform(get("/api/hello"))
                .andExpect(status().isOk())
                .andExpect(content().string("Hello Spring Web"));
    }
}
```

This assumes `HelloController` is the controller from section 4. It tests the MVC layer without starting a real HTTP server.

For controller tests that depend on a service, provide a mock service bean using the test-mocking facilities supported by your Spring Boot version.

### Testing approaches

|Test type|What it checks|
|---|---|
|Unit test|Business logic in isolation|
|`@WebMvcTest`|MVC mappings, binding, validation and responses|
|`@SpringBootTest`|Application context and integrated components|
|`MockMvc`|HTTP-like request processing without a real server|
|`TestRestTemplate` or HTTP client|Calls a running test server|
|Integration test|API + database + other relevant components|

Test success cases and failure cases: invalid JSON, missing fields, nonexistent IDs, forbidden requests, malformed query parameters and unexpected service failures.

## 24. Configuration and environment-specific settings

Spring Web behavior can be configured in `application.properties`.

```
server.port=8080

spring.application.name=product-api

spring.servlet.multipart.max-file-size=10MB
spring.servlet.multipart.max-request-size=12MB

server.error.include-message=never
server.error.include-stacktrace=never
```

The last two settings are defensive defaults for error responses. Your custom exception handler should also avoid leaking internal details.

You can use environment variables for deployment-specific settings:

```
server.port=${SERVER_PORT:8080}
```

Here, Spring uses `SERVER_PORT` if defined; otherwise, it uses `8080`.

For example:

```
SERVER_PORT=9090 ./mvnw spring-boot:run
```

In a production deployment, externalize secrets and database credentials. Do not commit passwords, API keys or signing keys to Git.

## 25. Production-ready Spring Web architecture

For your Spring Boot projects, a clean structure might be:

```js
com.example.productapi/
├── ProductApiApplication.java
├── controller/
│   └── ProductController.java
├── service/
│   └── ProductService.java
├── repository/
│   └── ProductRepository.java
├── entity/
│   └── Product.java
├── dto/
│   ├── CreateProductRequest.java
│   └── ProductResponse.java
├── mapper/
│   └── ProductMapper.java
├── exception/
│   ├── GlobalExceptionHandler.java
│   └── ResourceNotFoundException.java
├── config/
│   ├── WebConfig.java
│   └── SecurityConfig.java
└── filter/
    └── RequestIdFilter.java
```

