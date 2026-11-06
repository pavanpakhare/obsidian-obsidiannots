[[Spring Boot]]
In Spring Boot, `ResponseEntity<T>` is used to control the **HTTP response body, status code, and headers**.

### Important `ResponseEntity` methods

| Method                  | Purpose                       | Example                                        |
| ----------------------- | ----------------------------- | ---------------------------------------------- |
| `ok()`                  | HTTP 200                      | `ResponseEntity.ok()`                          |
| `ok(body)`              | 200 + body                    | `ResponseEntity.ok(user)`                      |
| `status()`              | Custom status                 | `ResponseEntity.status(201)`                   |
| `created()`             | HTTP 201 Created              | `ResponseEntity.created(uri)`                  |
| `noContent()`           | HTTP 204                      | `ResponseEntity.noContent().build()`           |
| `badRequest()`          | HTTP 400                      | `ResponseEntity.badRequest().build()`          |
| `notFound()`            | HTTP 404                      | `ResponseEntity.notFound().build()`            |
| `internalServerError()` | HTTP 500                      | `ResponseEntity.internalServerError().build()` |
| `of(Optional)`          | 200 if present, otherwise 404 | `ResponseEntity.of(user)`                      |
| `status(HttpStatus)`    | Custom HTTP status            | `ResponseEntity.status(HttpStatus.ACCEPTED)`   |
| `header()`              | Add response header           | `.header("X-App", "Demo")`                     |
| `headers()`             | Add multiple headers          | `.headers(headers)`                            |
| `body()`                | Set response body             | `.body(user)`                                  |
| `build()`               | Build response without body   | `.build()`                                     |

### 1. Basic response

```java
@GetMapping("/users/1")
public ResponseEntity<User> getUser() {
    User user = new User("Pavan");

    return ResponseEntity.ok(user);
}
```

Response:

```http
HTTP/1.1 200 OK

{
    "name": "Pavan"
}
```

### 2. Different status codes

```java
return ResponseEntity.ok(user);              // 200
return ResponseEntity.created(uri).body(user); // 201
return ResponseEntity.noContent().build();   // 204
return ResponseEntity.badRequest().build();   // 400
return ResponseEntity.notFound().build();     // 404
return ResponseEntity.internalServerError().build(); // 500
```

### 3. `status()`

Useful when you need a specific status:

```java
return ResponseEntity
        .status(HttpStatus.ACCEPTED)
        .body(user);
```

Or:

```java
return ResponseEntity
        .status(202)
        .body(user);
```

### 4. Headers

```java
return ResponseEntity
        .ok()
        .header("X-App-Version", "1.0")
        .body(user);
```

You can add multiple headers:

```java
HttpHeaders headers = new HttpHeaders();
headers.add("X-App", "MyApp");
headers.add("X-Version", "1.0");

return ResponseEntity
        .ok()
        .headers(headers)
        .body(user);
```

### 5. `Optional` with `of()`

Very useful with Spring Data JPA:

```java
@GetMapping("/{id}")
public ResponseEntity<User> getUser(@PathVariable Long id) {

    Optional<User> user = userRepository.findById(id);

    return ResponseEntity.of(user);
}
```

It automatically produces:

```text
User exists     → 200 OK + User
User not found  → 404 Not Found
```

### 6. Builder pattern

`ResponseEntity` commonly follows:

```java
ResponseEntity
    .ok()
    .header(...)
    .body(...);
```

Think of it as:

```text
ResponseEntity
      │
      ├── HTTP Status
      │
      ├── Headers
      │
      └── Body
```

For Spring Boot REST APIs, the **most important methods to remember for interviews** are:

```java
ok()
ok(body)
status()
created()
noContent()
badRequest()
notFound()
internalServerError()
of()
header()
body()
build()
```

A useful next topic is **`ResponseEntity` vs `@ResponseBody` vs returning an object directly**—they're commonly confused in Spring Boot.
