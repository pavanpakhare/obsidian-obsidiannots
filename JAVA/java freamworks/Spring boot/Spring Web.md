[[Spring Boot]]

# Spring Boot Web 

Spring Boot Web is mainly used to build **web applications and REST APIs**. For interviews, understand the request flow, controllers, HTTP methods, request/response handling, validation, exception handling, and important annotations.

---

## 1. What is Spring Boot Web?

Spring Boot Web provides the infrastructure needed to create web applications using Spring MVC.

Typical architecture:

```text
Client
  ↓
HTTP Request
  ↓
DispatcherServlet
  ↓
Controller
  ↓
Service
  ↓
Repository
  ↓
Database
  ↓
Response
```

For a REST API:

```text
React / Postman / Mobile App
          ↓
       REST API
          ↓
    Spring Boot Web
          ↓
       Service
          ↓
        JPA
          ↓
     PostgreSQL
```

---

# 2. Main Dependency

For Maven:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

This gives you important Spring Web functionality, including:

- Spring MVC
    
- Embedded Tomcat
    
- REST support
    
- JSON conversion through Jackson
    
- HTTP request/response handling
    

---

# 3. `@SpringBootApplication`

Usually your application starts with:

```java
@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

`@SpringBootApplication` combines:

```java
@Configuration
@EnableAutoConfiguration
@ComponentScan
```

### Interview question

**Q: What does `@SpringBootApplication` do?**

It enables configuration, auto-configuration, and component scanning for the Spring Boot application.

---

# 4. `@RestController`

Used to create REST controllers.

```java
@RestController
@RequestMapping("/users")
public class UserController {

    @GetMapping
    public String getUsers() {
        return "Users";
    }
}
```

`@RestController` is essentially:

```java
@Controller
@ResponseBody
```

So the return value is written directly to the HTTP response.

---

# 5. `@Controller` vs `@RestController`

### `@Controller`

Normally used for MVC applications that return views.

```java
@Controller
public class HomeController {

    @GetMapping("/")
    public String home() {
        return "home";
    }
}
```

Here `"home"` can represent a view/template.

### `@RestController`

Used primarily for REST APIs.

```java
@RestController
public class UserController {

    @GetMapping("/users")
    public List<User> users() {
        return users;
    }
}
```

The object is converted to JSON.

---

# 6. `@RequestMapping`

Defines the URL mapping.

```java
@RestController
@RequestMapping("/api/users")
public class UserController {

}
```

Then:

```java
@GetMapping
public List<User> getUsers() {
    ...
}
```

maps to:

```text
GET /api/users
```

You can also specify methods directly:

```java
@RequestMapping(
    value = "/users",
    method = RequestMethod.GET
)
```

But normally:

```java
@GetMapping("/users")
@PostMapping("/users")
@PutMapping("/users/{id}")
@DeleteMapping("/users/{id}")
```

are preferred.

---

# 7. HTTP Methods

The most important methods for REST interviews:

|Method|Typical purpose|
|---|---|
|GET|Read data|
|POST|Create data|
|PUT|Replace/update resource|
|PATCH|Partial update|
|DELETE|Delete resource|

Example:

```java
@GetMapping("/users")
public List<User> getUsers() {
    return service.getUsers();
}
```

```java
@PostMapping("/users")
public User createUser(@RequestBody User user) {
    return service.createUser(user);
}
```

```java
@PutMapping("/users/{id}")
public User updateUser(
        @PathVariable Long id,
        @RequestBody User user) {

    return service.updateUser(id, user);
}
```

```java
@DeleteMapping("/users/{id}")
public void deleteUser(@PathVariable Long id) {
    service.deleteUser(id);
}
```

---

# 8. `@PathVariable`

Used when a value is part of the URL.

Request:

```text
GET /users/10
```

Code:

```java
@GetMapping("/users/{id}")
public User getUser(@PathVariable Long id) {
    return service.getUser(id);
}
```

Here:

```text
{id} → @PathVariable
```

---

# 9. `@RequestParam`

Used for query parameters.

Request:

```text
GET /users?page=1&size=10
```

Code:

```java
@GetMapping("/users")
public List<User> getUsers(
        @RequestParam int page,
        @RequestParam int size) {

    return service.getUsers(page, size);
}
```

You can provide defaults:

```java
@RequestParam(defaultValue = "0") int page
```

---

# 10. `@RequestBody`

Used to read JSON from the request body.

Request:

```json
{
    "name": "Pavan",
    "email": "pavan@example.com"
}
```

Controller:

```java
@PostMapping("/users")
public User createUser(@RequestBody User user) {
    return service.createUser(user);
}
```

Spring/Jackson converts:

```text
JSON
 ↓
Java Object
```

This process is called **deserialization**.

Response:

```text
Java Object
 ↓
JSON
```

is **serialization**.

---

# 11. `@ResponseBody`

Tells Spring to write the return value directly into the HTTP response body.

```java
@Controller
public class UserController {

    @GetMapping("/users")
    @ResponseBody
    public List<User> users() {
        return service.getUsers();
    }
}
```

`@RestController` automatically provides this behavior for controller methods.

---

# 12. `ResponseEntity`

One of the most important Spring Web classes for interviews.

It lets you control:

- HTTP status
    
- headers
    
- response body
    

Example:

```java
@GetMapping("/{id}")
public ResponseEntity<User> getUser(
        @PathVariable Long id) {

    User user = service.getUser(id);

    return ResponseEntity.ok(user);
}
```

For creation:

```java
return ResponseEntity
        .status(HttpStatus.CREATED)
        .body(user);
```

For not found:

```java
return ResponseEntity.notFound().build();
```

Example:

```java
@PostMapping
public ResponseEntity<User> createUser(
        @RequestBody User user) {

    User saved = service.createUser(user);

    return ResponseEntity
            .status(HttpStatus.CREATED)
            .body(saved);
}
```

---

# 13. Important HTTP Status Codes

Know these for interviews:

```text
200 OK
201 Created
204 No Content

400 Bad Request
401 Unauthorized
403 Forbidden
404 Not Found
409 Conflict

500 Internal Server Error
```

### Important distinction

```text
401 → authentication required/failed
403 → authenticated but not allowed
```

---

# 14. Request Flow in Spring Boot

Suppose the client sends:

```text
GET /api/users/10
```

The flow is approximately:

```text
Client
  ↓
Embedded Tomcat
  ↓
DispatcherServlet
  ↓
Handler Mapping
  ↓
Controller
  ↓
Service
  ↓
Repository
  ↓
Database
```

Response:

```text
Database
   ↓
Repository
   ↓
Service
   ↓
Controller
   ↓
HttpMessageConverter
   ↓
JSON
   ↓
Client
```

---

# 15. What is `DispatcherServlet`?

This is a **very important interview topic**.

`DispatcherServlet` is Spring MVC's **front controller**.

It receives incoming HTTP requests and coordinates the request processing.

Simplified:

```text
HTTP Request
     ↓
DispatcherServlet
     ↓
Find Controller
     ↓
Execute Controller
     ↓
Convert Response
     ↓
HTTP Response
```

### Interview question

**Q: What is DispatcherServlet?**

> DispatcherServlet is the front controller of Spring MVC. It receives incoming requests and delegates them to the appropriate handler/controller.

---

# 16. `HandlerMapping`

Spring needs to determine which controller method should handle a request.

For:

```text
GET /users/10
```

Spring finds:

```java
@GetMapping("/users/{id}")
public User getUser(...)
```

`HandlerMapping` helps map the request to the appropriate handler.

---

# 17. `HttpMessageConverter`

This is responsible for converting HTTP request/response bodies.

For example:

```text
JSON → Java Object
```

and:

```text
Java Object → JSON
```

Jackson commonly handles JSON conversion.

Example:

```json
{
    "id": 10,
    "name": "Pavan"
}
```

becomes:

```java
User user
```

---

# 18. Service Layer

Don't put all business logic inside the controller.

Bad:

```java
@PostMapping
public User createUser(@RequestBody User user) {

    // validation
    // business logic
    // database logic

    return repository.save(user);
}
```

Better:

```text
Controller
    ↓
Service
    ↓
Repository
```

Controller:

```java
@PostMapping
public User createUser(@RequestBody User user) {
    return userService.createUser(user);
}
```

Service:

```java
@Service
public class UserService {

    public User createUser(User user) {
        // business logic
        return repository.save(user);
    }
}
```

---

# 19. `@RequestHeader`

Read HTTP headers.

```java
@GetMapping("/profile")
public String profile(
        @RequestHeader("Authorization") String authorization) {

    return authorization;
}
```

Request:

```text
Authorization: Bearer eyJ...
```

This becomes important when implementing JWT authentication.

---

# 20. `@CookieValue`

Read a cookie:

```java
@GetMapping("/profile")
public String profile(
        @CookieValue("sessionId") String sessionId) {

    return sessionId;
}
```

---

# 21. `@ModelAttribute`

Commonly used for form/query parameter binding.

```java
@PostMapping("/users")
public String create(@ModelAttribute User user) {
    return "success";
}
```

It is especially relevant to traditional Spring MVC form handling.

---

# 22. Validation

Spring Web commonly works with Bean Validation.

Example:

```java
public class UserRequest {

    @NotBlank
    private String name;

    @Email
    private String email;

    @Size(min = 8)
    private String password;
}
```

Controller:

```java
@PostMapping
public User create(
        @Valid @RequestBody UserRequest request) {

    return service.create(request);
}
```

Important:

```java
@Valid
```

triggers validation.

---

# 23. Global Exception Handling

Instead of handling exceptions in every controller:

```java
try {
   ...
} catch (...) {
   ...
}
```

use:

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<String> handleUserNotFound(
            UserNotFoundException ex) {

        return ResponseEntity
                .status(HttpStatus.NOT_FOUND)
                .body(ex.getMessage());
    }
}
```

Flow:

```text
Controller
    ↓
Exception
    ↓
@RestControllerAdvice
    ↓
@ExceptionHandler
    ↓
HTTP Error Response
```

---

# 24. CORS

If your frontend runs on:

```text
http://localhost:3000
```

and Spring Boot runs on:

```text
http://localhost:8080
```

the browser may enforce CORS restrictions.

Simple controller-level example:

```java
@CrossOrigin("http://localhost:3000")
@RestController
public class UserController {
}
```

For larger applications, CORS is usually configured globally.

---

# 25. `application.properties`

Example:

```properties
server.port=8080
server.servlet.context-path=/api
```

Then:

```java
@GetMapping("/users")
```

can be accessed through:

```text
http://localhost:8080/api/users
```

Useful server properties include:

```properties
server.port=8081
server.address=0.0.0.0
server.servlet.context-path=/api
```

---

# 26. Complete Mini REST API

### Controller

```java
@RestController
@RequestMapping("/api/users")
public class UserController {

    private final UserService service;

    public UserController(UserService service) {
        this.service = service;
    }

    @GetMapping("/{id}")
    public ResponseEntity<User> getUser(
            @PathVariable Long id) {

        User user = service.getUser(id);

        return ResponseEntity.ok(user);
    }

    @PostMapping
    public ResponseEntity<User> createUser(
            @Valid @RequestBody UserRequest request) {

        User user = service.createUser(request);

        return ResponseEntity
                .status(HttpStatus.CREATED)
                .body(user);
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteUser(
            @PathVariable Long id) {

        service.deleteUser(id);

        return ResponseEntity.noContent().build();
    }
}
```

The API becomes:

```text
GET    /api/users/{id}
POST   /api/users
DELETE /api/users/{id}
```

---

# 27. Most Important Interview Questions

### Beginner

1. What is Spring Boot Web?
    
2. What is Spring MVC?
    
3. What is `@RestController`?
    
4. Difference between `@Controller` and `@RestController`?
    
5. What is `@RequestMapping`?
    
6. Difference between `@GetMapping` and `@PostMapping`?
    
7. What is `@RequestBody`?
    
8. What is `@PathVariable`?
    
9. What is `@RequestParam`?
    
10. What is `ResponseEntity`?
    

### Intermediate

11. What is `DispatcherServlet`?
    
12. Explain Spring MVC request lifecycle.
    
13. What is `HandlerMapping`?
    
14. What is `HttpMessageConverter`?
    
15. How does JSON conversion happen?
    
16. How do you handle exceptions globally?
    
17. What is `@RestControllerAdvice`?
    
18. What is `@ExceptionHandler`?
    
19. How does validation work?
    
20. What is CORS?
    

### Advanced

21. Explain the complete request lifecycle in Spring MVC.
    
22. How does Spring select a controller method?
    
23. How does Jackson integrate with Spring Boot?
    
24. Difference between filter and interceptor?
    
25. How does authentication fit into the request flow?
    
26. How does JWT authentication work with Spring Web?
    
27. How would you design a REST API for an e-commerce application?
    
28. How would you handle API errors consistently?
    
29. How would you implement pagination and sorting?
    
30. How would you version a REST API?
    

---

## The interview mental model

Remember this:

```text
HTTP Request
     ↓
Servlet Container (Tomcat)
     ↓
DispatcherServlet
     ↓
HandlerMapping
     ↓
Controller
     ↓
Service
     ↓
Repository
     ↓
Database
     ↓
Repository
     ↓
Service
     ↓
Controller
     ↓
HttpMessageConverter
     ↓
JSON Response
```

If you're preparing for **Java + Spring Boot interviews**, the next important layer after this is **Spring Boot REST API + JPA + Validation + Exception Handling + Spring Security/JWT**, because interviewers commonly connect these topics together.