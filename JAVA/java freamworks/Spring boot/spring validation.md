[[Spring Boot]]
# Spring Boot Validation

Spring Validation is used to **check whether incoming data is valid before your application processes it**.

For example, when creating a user:

```json
{
  "name": "",
  "email": "abc",
  "age": 15
}
```

You may want to enforce:

- Name must not be empty
    
- Email must be valid
    
- Age must be at least 18
    

Spring Boot can handle this using **Bean Validation**.

---

## 1. Add Validation Dependency

For Maven:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

Spring Boot uses **Jakarta Bean Validation** APIs.

---

# 2. Create a DTO

Suppose we have a registration API.

```java
public class UserRequest {

    @NotBlank(message = "Name is required")
    private String name;

    @Email(message = "Invalid email")
    @NotBlank(message = "Email is required")
    private String email;

    @Min(value = 18, message = "Age must be at least 18")
    private int age;

    // getters and setters
}
```

Here the annotations define our validation rules.

---

# 3. Common Validation Annotations

### `@NotNull`

Value cannot be `null`.

```java
@NotNull
private String name;
```

But:

```java
""
```

is allowed.

---

### `@NotEmpty`

Cannot be `null` or empty.

```java
@NotEmpty
private String name;
```

But whitespace may still be accepted.

---

### `@NotBlank`

Cannot be:

- `null`
    
- empty
    
- only whitespace
    

```java
@NotBlank
private String name;
```

For Strings, `@NotBlank` is often preferable to `@NotEmpty`.

---

### `@Email`

Checks email format.

```java
@Email
private String email;
```

Example:

```text
user@gmail.com    ✅
hello             ❌
```

---

### `@Size`

Controls length or collection size.

```java
@Size(min = 8, max = 20)
private String password;
```

---

### `@Min` / `@Max`

```java
@Min(18)
@Max(100)
private int age;
```

---

### `@Positive`

Must be greater than zero.

```java
@Positive
private int quantity;
```

---

### `@PositiveOrZero`

```java
@PositiveOrZero
private double price;
```

Allows:

```text
0
10
50
```

but not:

```text
-10
```

---

### `@Pattern`

Useful for custom formats.

```java
@Pattern(
    regexp = "^[0-9]{10}$",
    message = "Phone number must contain 10 digits"
)
private String phone;
```

---

# 4. Use `@Valid` in Controller

This is one of the **most important Spring Validation interview concepts**.

```java
@PostMapping("/users")
public ResponseEntity<String> createUser(
        @Valid @RequestBody UserRequest request) {

    return ResponseEntity.ok("User created");
}
```

The important part:

```java
@Valid
```

It tells Spring:

> Validate this request object before calling the method.

So if the request contains invalid data, the controller method won't execute normally.

---

# 5. Example Request

Suppose:

```java
public class UserRequest {

    @NotBlank(message = "Name is required")
    private String name;

    @Email(message = "Invalid email")
    @NotBlank(message = "Email is required")
    private String email;

    @Min(value = 18, message = "Age must be at least 18")
    private int age;
}
```

Request:

```json
{
    "name": "",
    "email": "abc",
    "age": 15
}
```

Validation detects:

```text
Name is required
Invalid email
Age must be at least 18
```

---

# 6. Handling Validation Errors

By default, Spring can return an error response, but in real applications we usually create our own response format.

Example:

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<Map<String, String>> handleValidation(
            MethodArgumentNotValidException ex) {

        Map<String, String> errors = new HashMap<>();

        ex.getBindingResult()
          .getFieldErrors()
          .forEach(error ->
              errors.put(error.getField(), error.getDefaultMessage())
          );

        return ResponseEntity.badRequest().body(errors);
    }
}
```

Now an invalid request can produce:

```json
{
  "name": "Name is required",
  "email": "Invalid email",
  "age": "Age must be at least 18"
}
```

This is much cleaner for frontend applications.

---

# 7. Validation Flow

The overall flow is:

```text
Client
   |
   | POST /users
   ↓
@RequestBody
   |
   ↓
DTO
   |
   ↓
@Valid
   |
   ↓
Bean Validation
   |
   ├── Valid ─────→ Controller
   |
   └── Invalid ───→ Exception
                         |
                         ↓
                @RestControllerAdvice
                         |
                         ↓
                    Error Response
```

---

# 8. `@Valid` vs `@Validated`

This is a common interview question.

### `@Valid`

Comes from Jakarta Validation:

```java
import jakarta.validation.Valid;
```

Used for standard validation.

```java
@Valid
@RequestBody UserRequest request
```

---

### `@Validated`

Comes from Spring:

```java
import org.springframework.validation.annotation.Validated;
```

It supports **validation groups** and is also useful for method-level validation.

Example:

```java
@Validated
@RestController
public class UserController {
}
```

For basic DTO validation, you'll commonly see:

```java
@Valid
```

---

# 9. Nested Object Validation

Suppose:

```java
public class UserRequest {

    @NotBlank
    private String name;

    @Valid
    private AddressRequest address;
}
```

And:

```java
public class AddressRequest {

    @NotBlank
    private String city;

    @NotBlank
    private String pincode;
}
```

The `@Valid` on `address` tells Bean Validation to **validate the nested object too**.

---

# 10. Validation Groups

Sometimes different operations need different validation rules.

For example:

```text
Create User
    ↓
password required

Update User
    ↓
password optional
```

You can use validation groups.

```java
public interface Create {}
public interface Update {}
```

DTO:

```java
@NotBlank(groups = Create.class)
private String password;
```

Then:

```java
@Validated(Create.class)
```

can apply the `Create` rules.

---

# 11. Custom Validation

Sometimes built-in annotations aren't enough.

For example:

```text
Username must not contain "admin"
```

You can create your own annotation:

```java
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
@Constraint(validatedBy = UsernameValidator.class)
public @interface ValidUsername {

    String message() default "Invalid username";

    Class<?>[] groups() default {};

    Class<? extends Payload>[] payload() default {};
}
```

Then create:

```java
public class UsernameValidator
        implements ConstraintValidator<ValidUsername, String> {

    @Override
    public boolean isValid(String value,
                           ConstraintValidatorContext context) {

        return value != null &&
               !value.toLowerCase().contains("admin");
    }
}
```

Use it:

```java
@ValidUsername
private String username;
```

---

# 12. Validation vs Database Constraints

This distinction is important.

### Validation

Checks data at the application/API layer.

```java
@NotBlank
private String username;
```

### Database constraint

Protects data at the database layer.

```sql
username VARCHAR(100) NOT NULL
```

You generally want **both**.

```text
Client
   ↓
Spring Validation
   ↓
Business Logic
   ↓
JPA/Hibernate
   ↓
Database Constraints
```

Validation provides a good API experience, while database constraints provide data integrity.

---

# 13. DTO vs Entity Validation

You can technically put validation annotations on entities:

```java
@Entity
public class User {

    @NotBlank
    private String name;
}
```

But for REST APIs, it's often cleaner to use **DTOs**:

```text
HTTP Request
     ↓
UserRequest DTO
     ↓
Validation
     ↓
User Entity
     ↓
Database
```

This prevents your API validation requirements from being tightly coupled to your database model.

---

# Interview Questions

### 1. What is Spring Validation?

It is a mechanism for validating incoming data using Jakarta Bean Validation annotations and Spring integration.

### 2. What does `@Valid` do?

It triggers validation of an object according to its validation annotations.

### 3. Difference between `@NotNull`, `@NotEmpty`, and `@NotBlank`?

|Annotation|null|empty `""`|whitespace|
|---|--:|--:|--:|
|`@NotNull`|❌|✅|✅|
|`@NotEmpty`|❌|❌|✅|
|`@NotBlank`|❌|❌|❌|

### 4. What exception occurs for invalid `@RequestBody`?

Typically:

```java
MethodArgumentNotValidException
```

### 5. How do you handle validation errors globally?

Use:

```java
@RestControllerAdvice
```

with:

```java
@ExceptionHandler(MethodArgumentNotValidException.class)
```

### 6. `@Valid` vs `@Validated`?

`@Valid` provides standard Bean Validation cascading; `@Validated` is Spring's variant and supports validation groups and method-level validation.

### 7. Why use DTO validation?

To validate API input independently of the persistence/entity model.

---

## What you should remember for interviews

```text
spring-boot-starter-validation
          ↓
Jakarta Validation
          ↓
@NotBlank
@NotNull
@NotEmpty
@Email
@Size
@Min / @Max
@Pattern
          ↓
        @Valid
          ↓
Validation error
          ↓
MethodArgumentNotValidException
          ↓
@RestControllerAdvice
```

**Most important practical pattern:**

```java
@PostMapping
public ResponseEntity<?> create(
        @Valid @RequestBody UserRequest request) {
    // business logic
}
```

plus a global:

```java
@RestControllerAdvice
```

for returning clean validation errors.