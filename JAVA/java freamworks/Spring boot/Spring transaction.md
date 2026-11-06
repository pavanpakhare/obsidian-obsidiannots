[[Spring Boot]]
# Spring Transactions 

A **transaction** is a group of database operations that should be treated as **one unit of work**.

For example, in a banking application:

```text
Account A: -₹1000
Account B: +₹1000
```

Both operations should succeed together. If the second operation fails, the first should also be rolled back.

---

## 1. What is a Transaction?

Suppose we have:

```java
public void transferMoney(Long from, Long to, double amount) {
    debit(from, amount);
    credit(to, amount);
}
```

Without a transaction:

```text
debit()  → SUCCESS
credit() → FAILED

Database:
A = money deducted
B = money not received
```

With a transaction:

```text
BEGIN TRANSACTION

debit()
credit()

COMMIT
```

If something fails:

```text
BEGIN TRANSACTION

debit()
credit() → FAILED

ROLLBACK
```

So the database returns to its previous state.

---

# 2. ACID Properties

Transactions are commonly explained using **ACID**.

|Property|Meaning|
|---|---|
|**Atomicity**|All operations succeed or all are rolled back|
|**Consistency**|Database remains in a valid state|
|**Isolation**|Concurrent transactions don't improperly interfere|
|**Durability**|Committed data survives failures|

### Interview answer

> A transaction provides atomicity, consistency, isolation and durability for a group of database operations.

---

# 3. `@Transactional`

Spring provides:

```java
@Transactional
```

Example:

```java
@Service
public class BankService {

    @Transactional
    public void transferMoney(
            Long from,
            Long to,
            BigDecimal amount) {

        accountRepository.debit(from, amount);
        accountRepository.credit(to, amount);
    }
}
```

Conceptually Spring does:

```text
BEGIN
   ↓
debit()
   ↓
credit()
   ↓
COMMIT
```

If a qualifying exception occurs:

```text
BEGIN
   ↓
debit()
   ↓
credit() ❌
   ↓
ROLLBACK
```

---

# 4. Where Should `@Transactional` Be Used?

Usually put it on the **service layer**.

```java
@Service
public class OrderService {

    @Transactional
    public void placeOrder(OrderRequest request) {

        orderRepository.save(order);

        paymentRepository.save(payment);

        inventoryRepository.reduceStock(request.productId());
    }
}
```

Why service layer?

Because one business operation may involve multiple repository/database operations.

```text
Controller
    ↓
Service       ← Transaction boundary
    ↓
Repository
    ↓
Database
```

Avoid making every repository method independently transactional when the business operation needs several operations to be atomic.

---

# 5. `@Transactional` on Class vs Method

### Method

```java
@Transactional
public void createOrder() {
}
```

Only that method gets the transaction.

### Class

```java
@Transactional
@Service
public class OrderService {

    public void createOrder() {
    }

    public void cancelOrder() {
    }
}
```

All applicable public methods inherit the transaction configuration.

A method-level annotation can override the class-level configuration.

---

# 6. Rollback

This is a very common interview topic.

By default, Spring rolls back for:

```text
RuntimeException
Error
```

Example:

```java
@Transactional
public void transfer() {

    debit();

    throw new RuntimeException("Payment failed");

    // credit();
}
```

The transaction is rolled back.

---

## Checked Exception

Consider:

```java
@Transactional
public void transfer() throws IOException {

    debit();

    throw new IOException();
}
```

By default, a checked exception does **not** trigger rollback in Spring's standard transaction configuration.

You can explicitly configure it:

```java
@Transactional(rollbackFor = IOException.class)
public void transfer() throws IOException {
    debit();
    throw new IOException();
}
```

### Interview question

**Q: Does `@Transactional` rollback for checked exceptions?**

**Answer:**

> By default, Spring rolls back transactions for unchecked exceptions such as `RuntimeException` and `Error`, but not checked exceptions. `rollbackFor` can be used to configure rollback for checked exceptions.

---

# 7. `rollbackFor`

```java
@Transactional(
    rollbackFor = Exception.class
)
public void process() throws Exception {
    // ...
}
```

You can also specify particular exceptions:

```java
@Transactional(
    rollbackFor = PaymentException.class
)
```

---

# 8. `noRollbackFor`

Sometimes you don't want rollback for a particular exception.

```java
@Transactional(
    noRollbackFor = PaymentWarningException.class
)
public void process() {
}
```

---

# 9. Propagation

Propagation determines **what happens when a transactional method calls another transactional method**.

This is one of the most important Spring transaction interview topics.

Common propagation types:

```text
REQUIRED
REQUIRES_NEW
SUPPORTS
MANDATORY
NOT_SUPPORTED
NEVER
NESTED
```

---

## `REQUIRED`

Default propagation.

```java
@Transactional
public void methodA() {
    methodB();
}
```

```java
@Transactional(propagation = Propagation.REQUIRED)
public void methodB() {
}
```

If `methodA()` already has a transaction:

```text
methodA
   ↓
Transaction 1
   ↓
methodB
   ↓
uses Transaction 1
```

If there isn't one, Spring creates one.

### Interview answer

> `REQUIRED` joins the existing transaction if one exists; otherwise it creates a new transaction.

---

# 10. `REQUIRES_NEW`

Always creates a new transaction.

```java
@Transactional
public void methodA() {
    methodB();
}
```

```java
@Transactional(propagation = Propagation.REQUIRES_NEW)
public void methodB() {
}
```

Conceptually:

```text
Transaction A
    ↓
methodB()
    ↓
suspend A
    ↓
Transaction B
    ↓
commit B
    ↓
resume A
```

This is useful when an operation should have an independent transaction.

For example:

```text
Main transaction
    ↓
Save order
    ↓
Save audit → separate transaction
```

---

# 11. `REQUIRED` vs `REQUIRES_NEW`

|REQUIRED|REQUIRES_NEW|
|---|---|
|Joins existing transaction|Creates new transaction|
|Creates one if none exists|Suspends existing transaction|
|Same transaction|Independent transaction|
|Default|Explicitly configured|

Interview question:

> If outer transaction rolls back, what happens to `REQUIRES_NEW`?

The inner transaction may already have committed independently, so its committed changes are not automatically rolled back with the outer transaction.

---

# 12. Isolation Levels

Isolation controls how one transaction sees changes made by other concurrent transactions.

Spring:

```java
@Transactional(
    isolation = Isolation.READ_COMMITTED
)
```

Common levels:

```text
READ_UNCOMMITTED
READ_COMMITTED
REPEATABLE_READ
SERIALIZABLE
```

---

## READ_UNCOMMITTED

Can read uncommitted changes.

Possible problem:

```text
Transaction A:
UPDATE balance = 500

Transaction B:
READ balance → 500

Transaction A:
ROLLBACK
```

B saw data that was never committed.

This is called a:

**Dirty Read**

---

# 13. READ_COMMITTED

Only committed data can be read.

Prevents:

```text
Dirty Read
```

But another transaction can modify data between two reads.

Example:

```text
Transaction A:
READ → ₹1000

Transaction B:
UPDATE → ₹500
COMMIT

Transaction A:
READ → ₹500
```

This can produce a:

**Non-repeatable Read**

---

# 14. REPEATABLE_READ

If a transaction reads a row, repeated reads generally see the same committed version under the database's implementation.

It helps prevent:

```text
Non-repeatable Read
```

---

# 15. SERIALIZABLE

Highest standard isolation level.

Transactions behave as though they are executed serially.

```text
Transaction A
     ↓
Transaction B waits
     ↓
A completes
     ↓
B executes
```

Advantages:

- Strong consistency
    

Disadvantages:

- Lower concurrency
    
- More locking/contention
    
- Potential performance problems
    

---

# 16. Isolation Problems

Remember these for interviews:

### Dirty Read

Reading uncommitted data.

```text
A writes
 ↓
B reads
 ↓
A rollback
```

### Non-repeatable Read

Same row produces different values during one transaction.

```text
A reads → 100
B updates → 200
A reads → 200
```

### Phantom Read

Repeated query returns a different set of rows because another transaction inserted/deleted matching rows.

```sql
SELECT * FROM employees WHERE salary > 50000;
```

First:

```text
10 rows
```

Later:

```text
11 rows
```

because another transaction inserted a matching row.

---

# 17. Propagation vs Isolation

Very common interview confusion.

### Propagation

Answers:

> **How should this method participate in a transaction?**

Example:

```java
Propagation.REQUIRED
Propagation.REQUIRES_NEW
```

### Isolation

Answers:

> **How isolated should this transaction be from other concurrent transactions?**

Example:

```java
Isolation.READ_COMMITTED
Isolation.SERIALIZABLE
```

---

# 18. Read-Only Transaction

You can specify:

```java
@Transactional(readOnly = true)
public List<Product> getProducts() {
    return productRepository.findAll();
}
```

It indicates that the transaction is intended for reading.

Useful for:

```text
SELECT operations
```

Don't assume it universally guarantees that writes are impossible. Its exact effect depends on the transaction manager and database/JPA configuration.

---

# 19. Transaction Timeout

You can specify a timeout:

```java
@Transactional(timeout = 10)
public void processOrder() {
}
```

The transaction is given a timeout of approximately 10 seconds, subject to the transaction infrastructure.

Useful for preventing transactions from remaining active indefinitely.

---

# 20. Spring Transaction Architecture

A useful interview-level picture:

```text
Client
  ↓
Controller
  ↓
Service
  ↓
@Transactional
  ↓
Spring Transaction Interceptor
  ↓
Transaction Manager
  ↓
DataSource / JPA
  ↓
Database
```

For JPA, commonly:

```text
@Transactional
      ↓
JpaTransactionManager
      ↓
EntityManager
      ↓
Hibernate
      ↓
JDBC
      ↓
Database
```

---

# 21. How Does `@Transactional` Actually Work?

This is a **very important interview question**.

Spring generally uses **AOP/proxies** around the transactional method.

Conceptually:

```text
Your object
   ↑
Spring Proxy
   ↓
BEGIN TRANSACTION
   ↓
Your method
   ↓
COMMIT / ROLLBACK
```

For example:

```java
@Transactional
public void saveOrder() {
    repository.save(order);
}
```

Spring creates a proxy around the bean.

The proxy intercepts the call and manages the transaction.

---

# 22. Self-Invocation Problem

Very important.

Suppose:

```java
@Service
public class UserService {

    public void methodA() {
        methodB();
    }

    @Transactional
    public void methodB() {
        // database operation
    }
}
```

Calling:

```java
userService.methodA();
```

means:

```text
Proxy
 ↓
methodA()
 ↓
this.methodB()
```

The internal call to `methodB()` doesn't go through the Spring proxy.

Therefore, the `@Transactional` interception on `methodB()` may not happen as you expect.

### Common solution

Move the transactional method to another Spring bean:

```java
@Service
class UserService {

    private final TransactionService transactionService;

    public void methodA() {
        transactionService.methodB();
    }
}
```

---

# 23. Transaction Boundary Example

Imagine an e-commerce application:

```java
@Transactional
public void placeOrder(Order order) {

    orderRepository.save(order);

    paymentRepository.save(payment);

    inventoryRepository.decreaseStock(
        order.getProductId()
    );
}
```

Desired behavior:

```text
Order saved        ✓
Payment saved      ✓
Stock decreased    ✓
        ↓
     COMMIT
```

If payment fails:

```text
Order saved        ✓
Payment            ✗

        ↓

ROLLBACK

Order              → reverted
Payment             → reverted
Stock               → unchanged
```

That's the primary purpose of a transaction boundary.

---

# 24. Common Interview Questions

### Q1. What is `@Transactional`?

> `@Transactional` tells Spring to execute a method within a transaction managed by Spring's transaction infrastructure.

### Q2. Where should `@Transactional` generally be placed?

> Usually at the service layer where a business operation spans multiple database operations.

### Q3. What is the default propagation?

```java
Propagation.REQUIRED
```

### Q4. What causes rollback by default?

```text
RuntimeException
Error
```

Not checked exceptions by default.

### Q5. How do you rollback for checked exceptions?

```java
@Transactional(rollbackFor = Exception.class)
```

### Q6. `REQUIRED` vs `REQUIRES_NEW`?

```text
REQUIRED       → join existing transaction
REQUIRES_NEW   → create independent transaction
```

### Q7. What is isolation?

> Isolation determines how concurrently executing transactions interact and what changes made by other transactions are visible.

### Q8. What is dirty read?

> Reading uncommitted data from another transaction.

### Q9. How does `@Transactional` work internally?

> Spring commonly uses AOP proxies/interceptors to start, commit, and roll back transactions around the method invocation.

### Q10. Why doesn't `@Transactional` work during self-invocation?

> Because the internal method call bypasses the Spring proxy that normally applies transactional interception.

---

# 25. Most Important Things to Remember

For a Spring Boot interview, memorize this hierarchy:

```text
Spring Transaction
│
├── @Transactional
│
├── Transaction Manager
│
├── Propagation
│   ├── REQUIRED
│   ├── REQUIRES_NEW
│   ├── SUPPORTS
│   ├── MANDATORY
│   ├── NOT_SUPPORTED
│   ├── NEVER
│   └── NESTED
│
├── Isolation
│   ├── READ_UNCOMMITTED
│   ├── READ_COMMITTED
│   ├── REPEATABLE_READ
│   └── SERIALIZABLE
│
├── Rollback
│   ├── rollbackFor
│   └── noRollbackFor
│
├── readOnly
│
├── timeout
│
└── Spring AOP Proxy
```

**Highest-priority interview topics:** `@Transactional`, rollback rules, propagation, isolation, `REQUIRED` vs `REQUIRES_NEW`, transaction manager, AOP proxy, and self-invocation.