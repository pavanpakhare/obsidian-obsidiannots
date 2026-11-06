[[API DESIGN]]
# API Patterns

**API patterns** are common ways of designing APIs so they are consistent, scalable, maintainable, and easy for clients to use.

For REST APIs, these are the most important patterns to know:

### 1. Resource-Based API

Design endpoints around **resources**, not actions.

```http
GET    /users
GET    /users/10
POST   /users
PUT    /users/10
PATCH  /users/10
DELETE /users/10
```

Avoid:

```http
GET /getUsers
POST /createUser
POST /deleteUser
```

---

### 2. CRUD Pattern

Maps HTTP methods to database operations:

|HTTP|Purpose|Example|
|---|---|---|
|GET|Read|`GET /products/10`|
|POST|Create|`POST /products`|
|PUT|Replace|`PUT /products/10`|
|PATCH|Partial update|`PATCH /products/10`|
|DELETE|Delete|`DELETE /products/10`|

---

### 3. Nested Resource Pattern

Represent relationships between resources.

```http
GET /users/10/orders
GET /users/10/orders/50
```

Meaning:

```text
User 10
 └── Orders
      └── Order 50
```

Don't make nesting excessively deep:

```http
/users/10/orders/50/items/20/reviews/5
```

---

### 4. Query Parameter Pattern

Use query parameters for **filtering, sorting, searching, and pagination**.

```http
GET /products?category=phone
```

Multiple filters:

```http
GET /products?category=phone&brand=samsung
```

Sorting:

```http
GET /products?sort=price&direction=asc
```

Searching:

```http
GET /products?search=iphone
```

---

### 5. Pagination Pattern

Instead of returning thousands of records:

```http
GET /products?page=0&size=20
```

Response:

```json
{
  "content": [
    { "id": 1, "name": "Phone" }
  ],
  "page": 0,
  "size": 20,
  "totalElements": 250,
  "totalPages": 13
}
```

Common approaches:

```text
Offset pagination
    page=2&size=20

Cursor pagination
    ?cursor=eyJpZCI6MTAwfQ==
```

Cursor pagination is often better for very large/changing datasets.

---

### 6. HTTP Status Code Pattern

Use HTTP status codes to communicate the result.

```text
200 OK              → successful GET/PUT/PATCH
201 Created         → successful POST
204 No Content      → successful DELETE
400 Bad Request     → invalid request
401 Unauthorized    → authentication required/failed
403 Forbidden       → authenticated but not allowed
404 Not Found       → resource doesn't exist
409 Conflict        → conflicting operation
422 Unprocessable   → validation/business-rule error
500 Internal Error  → server error
```

---

### 7. Consistent Error Response

Don't return completely different error formats from different endpoints.

Example:

```json
{
  "timestamp": "2026-10-06T00:30:00Z",
  "status": 400,
  "error": "VALIDATION_ERROR",
  "message": "Invalid request",
  "path": "/api/users",
  "errors": [
    {
      "field": "email",
      "message": "Invalid email"
    }
  ]
}
```

In Spring Boot, this is commonly implemented with:

```java
@RestControllerAdvice
public class GlobalExceptionHandler {
}
```

---

### 8. DTO Pattern

Don't expose your database entity directly.

```text
Database Entity
      ↓
   Service
      ↓
    DTO
      ↓
     API
```

Example:

```java
public record UserResponse(
    Long id,
    String name,
    String email
) {}
```

Instead of returning:

```java
UserEntity
```

directly.

---

### 9. API Versioning Pattern

When you introduce breaking changes:

```http
/api/v1/users
/api/v2/users
```

Example:

```http
GET /api/v1/products
GET /api/v2/products
```

Other approaches include header or media-type versioning.

---

### 10. Idempotency Pattern

Important for operations such as payments.

A client sends:

```http
POST /payments
Idempotency-Key: abc123
```

If the request is accidentally sent twice:

```text
Request 1 → Payment created
Request 2 → Existing result returned
```

This prevents duplicate payments/orders.

---

### 11. HATEOAS Pattern

Response contains links to related operations.

```json
{
  "id": 10,
  "name": "Pavan",
  "_links": {
    "self": "/users/10",
    "orders": "/users/10/orders"
  }
}
```

Useful in some REST architectures, but not required for most Spring Boot APIs.

---

### 12. BFF — Backend for Frontend

Create different APIs for different clients:

```text
                 ┌── Web BFF
Frontend ────────┼── Mobile BFF
                 └── TV BFF
                       │
                       ↓
                Backend Services
```

For example:

```http
/web/home
/mobile/home
```

Each BFF returns data optimized for that client.

---

### 13. API Gateway Pattern

Put a gateway in front of multiple services:

```text
                    ┌── User Service
Client → API Gateway ├── Order Service
                    ├── Product Service
                    └── Payment Service
```

Gateway can handle:

- Authentication
    
- Rate limiting
    
- Routing
    
- Logging
    
- CORS
    
- Load balancing
    
- Request transformation
    

---

### 14. Rate Limiting Pattern

Limit how many requests a client can make.

```text
100 requests/minute
```

Example:

```http
GET /api/products
```

After the limit:

```http
429 Too Many Requests
```

Common algorithms:

```text
Token Bucket
Leaky Bucket
Fixed Window
Sliding Window
```

---

### 15. Async API Pattern

For long-running operations, don't keep the HTTP request waiting.

```http
POST /reports
```

Response:

```http
202 Accepted
```

```json
{
  "jobId": "12345",
  "status": "PROCESSING"
}
```

Client can later check:

```http
GET /reports/jobs/12345
```

Or use:

```text
WebSocket
SSE
Webhook
Message Queue
```

---

### 16. Webhook Pattern

Instead of repeatedly asking:

```text
"Did payment complete?"
"Did payment complete?"
"Did payment complete?"
```

Your server receives an event:

```http
POST /webhooks/payment
```

Payment provider → Your server:

```json
{
  "event": "payment.completed",
  "paymentId": "P1001"
}
```

---

### 17. Saga Pattern

Useful in **microservices** when one business transaction spans multiple services.

```text
Order
  ↓
Payment
  ↓
Inventory
  ↓
Shipping
```

If inventory fails:

```text
Inventory ❌
    ↓
Refund Payment
    ↓
Cancel Order
```

This avoids requiring one distributed database transaction.

---

### 18. Retry Pattern

For temporary failures:

```text
Request
   ↓
Failure
   ↓
Wait
   ↓
Retry
   ↓
Failure
   ↓
Wait longer
   ↓
Retry
```

Usually use **exponential backoff**:

```text
1 sec
2 sec
4 sec
8 sec
```

Add jitter to avoid many clients retrying simultaneously.

---

### 19. Circuit Breaker Pattern

Prevents repeatedly calling a failing service.

```text
Client
  ↓
Service A
  ↓
Service B ❌
```

After repeated failures:

```text
Service A
    ↓
Circuit OPEN
    ↓
Don't call Service B
```

Later it enters half-open state and tests whether the service recovered.

---

## Big Picture

These patterns often work together:

```text
                     Client
                       │
                       ↓
                 API Gateway
                       │
              Authentication
                       │
                 Rate Limiting
                       │
              ┌────────┴────────┐
              ↓                 ↓
          User API          Order API
              │                 │
             DTO               DTO
              │                 │
          Service             Service
              │                 │
              ↓                 ↓
          Database         Payment Service
                                │
                         Circuit Breaker
                                │
                         Message Queue
                                │
                           Async Worker
```

### For Spring Boot interviews, prioritize

**REST/resource design → HTTP methods/status codes → DTO → validation/error handling → pagination/filtering/sorting → authentication/JWT → API versioning → idempotency → rate limiting → API Gateway → async APIs → Webhooks → Saga → Circuit Breaker.**