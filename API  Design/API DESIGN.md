# API Design Patterns

API design patterns are **common, reusable approaches for designing APIs** so they are consistent, scalable, maintainable, and easy for clients to use.

For REST APIs, these are the most important patterns to know.

---

## 1. Resource-Oriented API

Design URLs around **resources**, not actions.

### ❌ Avoid

```http
GET /getUsers
POST /createUser
POST /deleteUser
```

### ✅ Prefer

```http
GET    /users
GET    /users/10
POST   /users
PUT    /users/10
PATCH  /users/10
DELETE /users/10
```

Think:

```text
Resource = User
URL      = /users
HTTP verb = operation
```

---

# 2. CRUD Pattern

Map HTTP methods to CRUD operations.

|Operation|HTTP|Endpoint|
|---|---|---|
|Create|POST|`/users`|
|Read all|GET|`/users`|
|Read one|GET|`/users/10`|
|Update|PUT|`/users/10`|
|Partial update|PATCH|`/users/10`|
|Delete|DELETE|`/users/10`|

Example:

```http
POST /products
```

```json
{
  "name": "Laptop",
  "price": 50000
}
```

Response:

```http
201 Created
```

```json
{
  "id": 101,
  "name": "Laptop",
  "price": 50000
}
```

---

# 3. Nested Resource Pattern

Use nesting when one resource belongs to another.

```http
GET /users/10/orders
```

Meaning:

> Get orders belonging to user 10.

Another example:

```http
GET /orders/500/items
```

```text
User
 └── Orders
      └── Items
```

Don't over-nest:

```http
/users/10/orders/500/items/2/reviews/5
```

Usually better to use separate endpoints when nesting becomes excessive.

---

# 4. Filtering Pattern

Use query parameters for filtering.

```http
GET /products?category=laptop
```

Multiple filters:

```http
GET /products?category=laptop&brand=dell&minPrice=30000
```

Example:

```http
GET /users?status=active&role=admin
```

---

# 5. Pagination Pattern

Never return millions of records at once.

```http
GET /products?page=0&size=20
```

Response:

```json
{
  "content": [
    {
      "id": 1,
      "name": "Laptop"
    }
  ],
  "page": 0,
  "size": 20,
  "totalElements": 150,
  "totalPages": 8
}
```

Common Spring Data JPA implementation:

```java
@GetMapping("/products")
public Page<Product> getProducts(
        Pageable pageable) {
    return repository.findAll(pageable);
}
```

---

# 6. Sorting Pattern

```http
GET /products?sort=price,asc
```

Multiple sorting:

```http
GET /products?sort=price,asc&sort=name,asc
```

Or:

```http
GET /products?sort=price:asc,name:desc
```

Choose one convention and keep it consistent.

---

# 7. Search Pattern

Use query parameters for search.

```http
GET /products?search=laptop
```

More complex:

```http
GET /products?search=gaming&category=laptop
```

For large search systems, the API may internally use something such as Elasticsearch.

```text
Client
  ↓
GET /products?search=laptop
  ↓
Spring Boot
  ↓
Search Service
  ↓
Elasticsearch
```

---

# 8. Partial Update Pattern

Use `PATCH` when only some properties should change.

```http
PATCH /users/10
```

```json
{
  "email": "new@example.com"
}
```

Only `email` changes.

Compare:

```http
PUT /users/10
```

Usually represents replacing/updating the complete resource representation.

---

# 9. Standard HTTP Status Codes

A good API uses HTTP status codes consistently.

```text
200 OK
201 Created
204 No Content

400 Bad Request
401 Unauthorized
403 Forbidden
404 Not Found
409 Conflict
422 Unprocessable Content

500 Internal Server Error
```

Example:

```http
GET /users/999
```

```http
404 Not Found
```

---

# 10. Standard Error Response Pattern

Instead of returning random error formats:

```json
{
  "error": "something went wrong"
}
```

use a consistent structure.

For example:

```json
{
  "status": 400,
  "error": "VALIDATION_ERROR",
  "message": "Invalid request",
  "path": "/users",
  "timestamp": "2026-10-06T00:50:00Z",
  "details": [
    {
      "field": "email",
      "message": "Invalid email"
    }
  ]
}
```

This becomes especially useful with Spring Boot's `@RestControllerAdvice`.

---

# 11. Versioning Pattern

APIs change over time.

### URL versioning

```http
/api/v1/users
/api/v2/users
```

Very common and easy to understand.

### Header versioning

```http
Accept: application/vnd.company.v2+json
```

### Query parameter

```http
/api/users?version=2
```

For most Spring Boot projects, URL versioning is simple:

```text
/api/v1/products
/api/v2/products
```

---

# 12. Idempotency Pattern

Important for payment/order APIs.

An operation is **idempotent** if repeating the same request doesn't create additional effects.

Example:

```http
POST /payments
Idempotency-Key: abc123
```

Client accidentally sends the request twice.

```text
Request 1 → Payment created
Request 2 → Existing payment returned
```

Without idempotency:

```text
₹1000 payment
₹1000 payment
```

With idempotency:

```text
₹1000 payment
```

This is extremely important for financial APIs.

---

# 13. HATEOAS Pattern

The response contains links to related actions/resources.

```json
{
  "id": 10,
  "name": "Pavan",
  "_links": {
    "self": {
      "href": "/users/10"
    },
    "orders": {
      "href": "/users/10/orders"
    }
  }
}
```

It's part of the broader REST architectural style, but many practical REST APIs don't use it heavily.

---

# 14. Bulk Operations Pattern

Instead of making 1,000 API calls:

```text
POST /users
POST /users
POST /users
...
```

provide a bulk endpoint:

```http
POST /users/bulk
```

```json
{
  "users": [
    {
      "name": "A"
    },
    {
      "name": "B"
    }
  ]
}
```

Useful for:

- imports
    
- batch processing
    
- data synchronization
    
- administrative operations
    

---

# 15. Async Operation Pattern

Some operations take a long time.

Don't keep the HTTP request open unnecessarily.

```http
POST /reports
```

Response:

```http
202 Accepted
```

```json
{
  "jobId": "job-123",
  "status": "PROCESSING"
}
```

Client can then check:

```http
GET /reports/jobs/job-123
```

```json
{
  "jobId": "job-123",
  "status": "COMPLETED",
  "downloadUrl": "/reports/job-123/download"
}
```

Architecture:

```text
Client
  │
  │ POST /reports
  ↓
API
  │
  ├── Create Job
  │
  └── Queue
       ↓
   Background Worker
       ↓
     Report
```

---

# 16. API Gateway Pattern

Common in microservices.

```text
                    ┌── User Service
                    │
Client → API Gateway ├── Order Service
                    │
                    ├── Product Service
                    │
                    └── Payment Service
```

The client doesn't need to know every microservice.

Gateway can handle:

- authentication
    
- authorization
    
- routing
    
- rate limiting
    
- logging
    
- load balancing
    
- request transformation
    

---

# 17. Backend-for-Frontend (BFF)

Different clients may need different APIs.

```text
                 ┌── Web BFF
                 │
Client → Gateway ├── Mobile BFF
                 │
                 └── Admin BFF
```

For example, mobile might need:

```json
{
  "name": "Laptop",
  "price": 50000
}
```

while the web application needs much more information.

Instead of making one giant API response work for everyone, use client-specific BFFs.

---

# 18. Rate Limiting Pattern

Protect your API from excessive requests.

Example:

```text
100 requests / minute / user
```

If exceeded:

```http
429 Too Many Requests
```

Often implemented using:

```text
API Gateway
     ↓
Rate Limiter
     ↓
Spring Boot API
```

Redis is commonly used for distributed rate limiting.

---

# 19. Authentication Pattern

Common API authentication:

```text
Client
   │
   │ Authorization: Bearer <JWT>
   ↓
Spring Security
   ↓
JWT validation
   ↓
Controller
```

Example:

```http
GET /users/me
Authorization: Bearer eyJ...
```

Spring Security then establishes the authenticated user.

---

# 20. DTO Pattern

Don't necessarily expose your JPA entity directly.

### Entity

```java
@Entity
class User {

    @Id
    private Long id;

    private String password;

    private String email;
}
```

### Response DTO

```java
public record UserResponse(
    Long id,
    String email
) {}
```

Controller:

```java
@GetMapping("/{id}")
public UserResponse getUser(@PathVariable Long id) {
    return userService.getUser(id);
}
```

This prevents things like:

```text
Database Entity
     ↓
Accidental password exposure ❌
```

Instead:

```text
Entity → Service → DTO → JSON
```

---

# 21. Envelope Pattern

Wrap responses inside a common object.

```json
{
  "data": {
    "id": 10,
    "name": "Pavan"
  },
  "meta": {}
}
```

For lists:

```json
{
  "data": [
    {
      "id": 1,
      "name": "A"
    },
    {
      "id": 2,
      "name": "B"
    }
  ],
  "meta": {
    "page": 0,
    "size": 20
  }
}
```

This is optional. Don't add envelopes without a reason.

---

# 22. ETag / Conditional Request Pattern

Useful for caching and preventing unnecessary data transfer.

```http
GET /products/10
If-None-Match: "abc123"
```

If unchanged:

```http
304 Not Modified
```

Useful for:

- browsers
    
- mobile apps
    
- CDN caching
    
- frequently requested resources
    

---

# 23. Optimistic Concurrency Pattern

Prevents one user from accidentally overwriting another user's changes.

Example:

```json
{
  "id": 10,
  "name": "Laptop",
  "version": 5
}
```

User updates version 5.

But database is already version 6.

```text
Client version: 5
Database version: 6

        ↓

409 Conflict
```

In JPA:

```java
@Version
private Long version;
```

---

# 24. API Composition Pattern

Sometimes one UI needs information from several services.

```text
                 ┌── User Service
                 │
Client → API → ──┼── Order Service
                 │
                 └── Product Service
```

API combines:

```json
{
  "user": {},
  "orders": [],
  "recommendations": []
}
```

Useful when the client shouldn't make many separate requests.

---

# 25. Webhook Pattern

Instead of constantly asking:

```text
"Did my payment finish?"
"Did my payment finish?"
"Did my payment finish?"
```

the server sends an event to your application.

```text
Payment Provider
      │
      │ POST /webhooks/payment
      ↓
Your Spring Boot API
```

Example:

```json
{
  "event": "payment.success",
  "paymentId": "pay_123"
}
```

---

# Important API Design Patterns to Learn

For **Java + Spring Boot backend development**, I'd prioritize them like this:

```text
                    API DESIGN
                       │
       ┌───────────────┼────────────────┐
       ↓               ↓                ↓
   REST Basics      Data Access      Reliability
       │               │                │
   Resources        Pagination       Idempotency
   CRUD             Filtering        Rate Limiting
   HTTP verbs       Sorting          Retry
   Status codes     Search           Timeout
   Versioning
       │
       ├── DTO
       ├── Error Handling
       ├── Authentication/JWT
       ├── Validation
       └── API Documentation
                       │
                 Microservices
                       │
             ┌─────────┼─────────┐
             ↓         ↓         ↓
         Gateway      BFF     Composition
             │
             └── Async APIs / Webhooks
```

### A good Spring Boot API architecture

```text
Client
  │
  ↓
Controller
  │
  ├── Validation
  │
  ↓
Service
  │
  ├── Business Logic
  │
  ↓
Repository
  │
  ↓
PostgreSQL
```

With cross-cutting components:

```text
              Spring Boot API
                    │
       ┌────────────┼────────────┐
       ↓            ↓            ↓
Spring Security  Exception    Logging
                  Handler
       │            │
       ↓            ↓
      JWT        Error DTO
```

**Interview tip:** Don't just memorize REST URLs. Be able to explain **why** you use `PATCH`, `202`, `409`, idempotency keys, pagination, DTOs, versioning, rate limiting, and API gateways. Those are the parts that distinguish basic CRUD API knowledge from real API design.