[[Spring Boot]]
# Spring Boot + JPA — Interview

For interviews, learn **JPA concepts first**, then how **Spring Data JPA** simplifies them.

## 1. What is JPA?

**JPA (Java Persistence API)** is a specification for mapping Java objects to relational database tables.

It lets you work with:

```text
Java Object  ↔  Database Table
Entity       ↔  Row
Field        ↔  Column
```

Example:

```java
@Entity
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private String email;
}
```

This can represent:

```text
users
----------------------
id | name | email
```

### Interview question

**Q: Is JPA a framework?**

**Answer:** No. JPA is a specification/API. Hibernate is a popular implementation of JPA.

```text
Spring Boot
    ↓
Spring Data JPA
    ↓
JPA
    ↓
Hibernate
    ↓
JDBC
    ↓
Database
```

---

# 2. What is Hibernate?

Hibernate is an **ORM (Object Relational Mapping)** framework.

ORM means mapping:

```text
Java Class       → Database Table
Java Object      → Database Row
Java Field       → Database Column
```

Hibernate handles much of the SQL generation and database interaction.

### Interview question

**Q: JPA vs Hibernate?**

|JPA|Hibernate|
|---|---|
|Specification|Implementation|
|Defines APIs/rules|Implements them|
|`@Entity`, `@Id`, etc.|Executes ORM operations|
|Can have different implementations|One specific ORM framework|

---

# 3. What is Spring Data JPA?

Spring Data JPA provides an easier programming model on top of JPA.

Instead of writing:

```java
EntityManager em;

em.persist(user);
em.find(User.class, id);
```

you can create:

```java
public interface UserRepository
        extends JpaRepository<User, Long> {
}
```

Then:

```java
userRepository.save(user);

userRepository.findById(1L);

userRepository.findAll();

userRepository.deleteById(1L);
```

This is one of the most important things to understand for interviews.

---

# 4. Entity

An entity is a Java class mapped to a database table.

```java
@Entity
@Table(name = "users")
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    private String email;
}
```

Important annotations:

```java
@Entity
@Table
@Id
@GeneratedValue
@Column
```

---

# 5. `@Entity`

```java
@Entity
public class User {
}
```

Tells JPA:

> This class should be managed as a persistent entity.

Normally an entity needs an identifier:

```java
@Id
private Long id;
```

### Interview question

**Q: Why do we need `@Id`?**

Every entity needs a unique identifier so JPA can distinguish one entity instance from another.

---

# 6. `@Table`

```java
@Entity
@Table(name = "users")
public class User {
}
```

Maps the entity to a specific table.

Without `@Table`, JPA usually derives the table name from the entity name according to its naming rules.

---

# 7. `@Column`

```java
@Column(name = "user_email", nullable = false, unique = true)
private String email;
```

Common properties:

```java
nullable
unique
length
name
insertable
updatable
```

Example:

```java
@Column(nullable = false, length = 100)
private String name;
```

---

# 8. Primary Key Generation

Most common:

```java
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long id;
```

Important strategies:

```java
IDENTITY
SEQUENCE
TABLE
AUTO
```

### `IDENTITY`

Database generates the ID.

Common with:

```text
MySQL
PostgreSQL identity columns
```

### `SEQUENCE`

Uses a database sequence.

Commonly used with PostgreSQL:

```java
@GeneratedValue(strategy = GenerationType.SEQUENCE)
```

### Interview question

**Q: IDENTITY vs SEQUENCE?**

`IDENTITY` relies on an identity/auto-increment column, while `SEQUENCE` obtains identifiers from a database sequence.

---

# 9. Repository

The most common repository:

```java
public interface UserRepository
        extends JpaRepository<User, Long> {
}
```

Hierarchy:

```text
Repository
    ↓
CrudRepository
    ↓
PagingAndSortingRepository
    ↓
JpaRepository
```

`JpaRepository` provides CRUD plus JPA-specific functionality.

---

# 10. Important `JpaRepository` Methods

### Save

```java
userRepository.save(user);
```

Used for creating/updating entities.

### Find all

```java
userRepository.findAll();
```

### Find by ID

```java
userRepository.findById(id);
```

Return type:

```java
Optional<User>
```

Example:

```java
Optional<User> user =
        userRepository.findById(1L);
```

### Delete

```java
userRepository.deleteById(id);
```

### Count

```java
long count = userRepository.count();
```

### Exists

```java
boolean exists =
        userRepository.existsById(id);
```

---

# 11. Derived Query Methods

One of the most important Spring Data JPA interview topics.

Suppose:

```java
@Entity
public class User {

    private String name;
    private String email;
}
```

You can write:

```java
List<User> findByName(String name);
```

Spring Data generates the query based on the method name.

### Examples

```java
findByName(String name)

findByEmail(String email)

findByNameAndEmail(String name, String email)

findByNameOrEmail(String name, String email)

findByAgeGreaterThan(int age)

findByAgeLessThan(int age)

findByNameContaining(String name)

findByNameStartingWith(String name)

findByNameEndingWith(String name)

findByEmailIgnoreCase(String email)
```

---

# 12. `@Query`

When derived query methods become complicated, use `@Query`.

### JPQL

```java
@Query("SELECT u FROM User u WHERE u.email = :email")
Optional<User> findUserByEmail(
        @Param("email") String email);
```

Important:

JPQL uses **entity and field names**, not necessarily database table/column names.

```text
User
u.email
```

rather than:

```text
users
user_email
```

---

# 13. Native Query

You can execute actual SQL:

```java
@Query(
    value = "SELECT * FROM users WHERE email = :email",
    nativeQuery = true
)
Optional<User> findUser(
        @Param("email") String email);
```

### JPQL vs Native SQL

```text
JPQL
↓
Entity-oriented

Native SQL
↓
Database-oriented
```

### Interview question

**Q: When would you use native queries?**

When database-specific SQL functionality is required or when an existing SQL query is difficult to express efficiently using JPQL.

---

# 14. Relationships

This is extremely important for interviews.

JPA supports:

```text
@OneToOne
@OneToMany
@ManyToOne
@ManyToMany
```

---

## 15. `@OneToOne`

Example:

```text
User ───── Passport
 1            1
```

```java
@OneToOne
@JoinColumn(name = "passport_id")
private Passport passport;
```

---

# 16. `@ManyToOne`

Example:

```text
Many Employees → One Department
```

```java
@ManyToOne
@JoinColumn(name = "department_id")
private Department department;
```

Database:

```text
employee
-----------------------
id
name
department_id
```

`department_id` is typically a foreign key.

---

# 17. `@OneToMany`

One department has many employees:

```java
@OneToMany(mappedBy = "department")
private List<Employee> employees;
```

Here:

```java
mappedBy = "department"
```

means the relationship is controlled by the `department` field in `Employee`.

---

# 18. `mappedBy`

Very common interview question.

```java
@OneToMany(mappedBy = "department")
private List<Employee> employees;
```

`mappedBy` tells JPA:

> This side is not the owner of the relationship.

The owning side is:

```java
@ManyToOne
@JoinColumn(name = "department_id")
private Department department;
```

### Easy way to remember

The side containing:

```java
@JoinColumn
```

is commonly the **owning side**.

---

# 19. `@ManyToMany`

Example:

```text
Student ↔ Course
```

A student can take many courses and a course can have many students.

```java
@ManyToMany
@JoinTable(
    name = "student_course",
    joinColumns = @JoinColumn(name = "student_id"),
    inverseJoinColumns = @JoinColumn(name = "course_id")
)
private Set<Course> courses;
```

Database:

```text
student
course
student_course
```

---

# 20. Fetch Type

Two major types:

```java
FetchType.LAZY
FetchType.EAGER
```

### LAZY

Data is loaded when needed.

```java
@ManyToOne(fetch = FetchType.LAZY)
private Department department;
```

Conceptually:

```text
Load Employee
     ↓
Department not immediately loaded
     ↓
Access employee.getDepartment()
     ↓
Hibernate loads Department
```

### EAGER

Related data is loaded immediately according to the ORM/provider's behavior.

### Interview question

**Q: Which is generally preferable for associations?**

`LAZY` is often preferred because it avoids unnecessarily loading related data, but the correct choice depends on the use case and query design.

---

# 21. N+1 Query Problem

Very important interview topic.

Suppose:

```java
List<Employee> employees =
        employeeRepository.findAll();
```

Then:

```java
for (Employee e : employees) {
    System.out.println(e.getDepartment().getName());
}
```

Potentially:

```text
1 query → employees

N queries → departments
```

Total:

```text
1 + N queries
```

This is the **N+1 problem**.

---

# 22. Solving N+1

One approach is `JOIN FETCH`.

```java
@Query("""
    SELECT e
    FROM Employee e
    JOIN FETCH e.department
""")
List<Employee> findEmployeesWithDepartment();
```

Another approach is `@EntityGraph`.

```java
@EntityGraph(attributePaths = {"department"})
List<Employee> findAll();
```

---

# 23. Cascade

Cascade determines whether operations on one entity are propagated to related entities.

Example:

```java
@OneToMany(
    mappedBy = "department",
    cascade = CascadeType.ALL
)
private List<Employee> employees;
```

Common cascade types:

```text
PERSIST
MERGE
REMOVE
REFRESH
DETACH
ALL
```

### Example

```java
cascade = CascadeType.PERSIST
```

Persisting the parent can persist related new entities.

---

# 24. `CascadeType.REMOVE`

```java
@OneToMany(
    mappedBy = "department",
    cascade = CascadeType.REMOVE
)
```

Deleting the parent can cause related entities to be deleted.

### Interview warning

Don't blindly use:

```java
CascadeType.ALL
```

Especially on relationships where deleting a parent should **not** delete independent child/business records.

---

# 25. Orphan Removal

```java
@OneToMany(
    mappedBy = "department",
    orphanRemoval = true
)
private List<Employee> employees;
```

If an employee is removed from the parent's collection, JPA can delete that orphan entity from the database.

Conceptually:

```java
department.getEmployees().remove(employee);
```

may result in:

```sql
DELETE FROM employee ...
```

depending on the persistence operation and mapping.

---

# 26. Entity Lifecycle

Very important.

An entity can be:

```text
Transient
   ↓
Managed/Persistent
   ↓
Detached
   ↓
Removed
```

### Transient

Object exists only in Java.

```java
User user = new User();
```

Not managed by persistence context.

### Managed

```java
entityManager.persist(user);
```

JPA manages the entity.

### Detached

Entity was once managed but is no longer associated with the current persistence context.

### Removed

Entity is marked for deletion.

---

# 27. Persistence Context

One of the most important JPA concepts.

Persistence context is a collection/context in which JPA manages entity instances.

Think:

```text
Persistence Context
        |
        +-- User #1
        +-- User #2
        +-- Order #1
```

Hibernate tracks changes to managed entities.

---

# 28. Dirty Checking

Suppose:

```java
@Transactional
public void updateUser(Long id) {

    User user = userRepository.findById(id)
            .orElseThrow();

    user.setName("Pavan");
}
```

Notice:

**No `save()` is necessarily required for the managed entity.**

Hibernate can detect the change through **dirty checking** and generate an SQL `UPDATE` during flush/transaction completion.

Conceptually:

```text
Database
   ↓
find()
   ↓
Managed Entity
   ↓
setName()
   ↓
Dirty Checking
   ↓
UPDATE
```

### Interview question

**Q: What is dirty checking?**

Hibernate detects changes made to managed entities and synchronizes those changes with the database during flushing.

---

# 29. First-Level Cache

JPA persistence context provides first-level caching.

Example:

```java
User u1 = entityManager.find(User.class, 1L);

User u2 = entityManager.find(User.class, 1L);
```

Within the same persistence context, JPA can return the already-managed entity rather than issuing another database query.

```text
Persistence Context
       ↓
   User ID = 1
```

First-level cache is associated with the persistence context.

---

# 30. Second-Level Cache

Second-level cache is optional and is associated with the persistence provider/session factory rather than one particular persistence context.

Possible implementations include Hibernate caching providers.

Interview distinction:

```text
1st Level Cache
→ Persistence Context
→ Mandatory JPA concept

2nd Level Cache
→ Optional
→ Shared across persistence contexts
```

---

# 31. Transaction

For database modifications, transactions are extremely important.

Spring:

```java
@Transactional
public void createOrder() {
    // database operations
}
```

Conceptually:

```text
BEGIN
   ↓
Operation 1
   ↓
Operation 2
   ↓
Operation 3
   ↓
COMMIT
```

If an appropriate failure occurs:

```text
ROLLBACK
```

---

# 32. `save()` vs `saveAndFlush()`

```java
repository.save(user);
```

Schedules/persists the entity through the persistence mechanism; SQL execution can occur later during flush.

```java
repository.saveAndFlush(user);
```

Saves and explicitly triggers a flush.

Important:

**Flush ≠ Commit**

```text
flush
↓
Synchronize persistence context with DB

commit
↓
Complete the transaction
```

---

# 33. `findById()` vs `getReferenceById()`

### `findById()`

```java
Optional<User> user =
        repository.findById(id);
```

Typically retrieves the entity and returns `Optional`.

### `getReferenceById()`

```java
User user =
        repository.getReferenceById(id);
```

Returns a reference/proxy that can defer database access until needed.

Useful when you need an entity reference, for example for setting a relationship, without immediately needing all its data.

---

# 34. Pagination

Don't load millions of records:

```java
findAll();
```

Use pagination:

```java
Pageable pageable =
        PageRequest.of(0, 10);

Page<User> page =
        userRepository.findAll(pageable);
```

Repository:

```java
public interface UserRepository
        extends JpaRepository<User, Long> {
}
```

Conceptually:

```text
Page 0 → 10 records
Page 1 → 10 records
Page 2 → 10 records
```

---

# 35. Sorting

```java
Sort sort =
        Sort.by("name").ascending();

List<User> users =
        userRepository.findAll(sort);
```

Descending:

```java
Sort.by("name").descending();
```

---

# 36. DTO Projection

Don't always return entities directly from APIs.

Entity:

```java
@Entity
public class User {
    private Long id;
    private String name;
    private String email;
}
```

DTO:

```java
public record UserDTO(
        Long id,
        String name
) {}
```

Query:

```java
@Query("""
    SELECT new com.example.dto.UserDTO(u.id, u.name)
    FROM User u
""")
List<UserDTO> findUserDTOs();
```

This can avoid fetching unnecessary entity data.

---

# 37. Optimistic Locking

Useful when multiple users may update the same database row.

```java
@Version
private Long version;
```

Conceptually:

```text
User A reads version 1
User B reads version 1

User A updates
version → 2

User B tries update using version 1
        ↓
conflict
```

This helps prevent lost updates.

---

# 38. Pessimistic Locking

Pessimistic locking obtains a database lock for the relevant data.

Example:

```java
@Lock(LockModeType.PESSIMISTIC_WRITE)
@Query("SELECT u FROM User u WHERE u.id = :id")
Optional<User> findUserForUpdate(
        @Param("id") Long id);
```

Conceptually:

```text
Transaction A
    ↓
locks row
    ↓
updates row

Transaction B
    ↓
waits/gets lock-related behavior
```

---

# 39. `@Transactional` + JPA

A common interview scenario:

```java
@Service
public class UserService {

    @Transactional
    public void updateUser(Long id) {

        User user = repository.findById(id)
                .orElseThrow();

        user.setName("John");
    }
}
```

Why does this work without:

```java
repository.save(user);
```

Because:

```text
@Transactional
       ↓
Persistence Context
       ↓
User becomes managed
       ↓
user.setName()
       ↓
Dirty Checking
       ↓
Flush
       ↓
UPDATE SQL
       ↓
Commit
```

---

# 40. Complete Spring Boot Example

### Entity

```java
@Entity
@Table(name = "users")
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String name;

    @Column(nullable = false, unique = true)
    private String email;

    // constructors, getters, setters
}
```

### Repository

```java
public interface UserRepository
        extends JpaRepository<User, Long> {

    Optional<User> findByEmail(String email);

    List<User> findByNameContainingIgnoreCase(String name);
}
```

### Service

```java
@Service
public class UserService {

    private final UserRepository repository;

    public UserService(UserRepository repository) {
        this.repository = repository;
    }

    @Transactional
    public User create(User user) {
        return repository.save(user);
    }

    @Transactional(readOnly = true)
    public User getUser(Long id) {
        return repository.findById(id)
                .orElseThrow();
    }

    @Transactional
    public void updateName(Long id, String name) {

        User user = repository.findById(id)
                .orElseThrow();

        user.setName(name);
    }
}
```

### Controller

```java
@RestController
@RequestMapping("/users")
public class UserController {

    private final UserService service;

    public UserController(UserService service) {
        this.service = service;
    }

    @GetMapping("/{id}")
    public User getUser(@PathVariable Long id) {
        return service.getUser(id);
    }

    @PostMapping
    public User create(@RequestBody User user) {
        return service.create(user);
    }
}
```

---

# 🔥 Most Important JPA Interview Questions

You should be able to answer these without looking at notes:

### Basic

1. What is JPA?
    
2. What is Hibernate?
    
3. JPA vs Hibernate?
    
4. What is Spring Data JPA?
    
5. What is ORM?
    
6. What is an entity?
    
7. Why is `@Id` required?
    
8. What does `@GeneratedValue` do?
    
9. What is `JpaRepository`?
    
10. `CrudRepository` vs `JpaRepository`?
    

### Queries

11. What are derived query methods?
    
12. What is `@Query`?
    
13. JPQL vs native SQL?
    
14. What is `@Param`?
    
15. How does pagination work?
    
16. How does sorting work?
    
17. What are projections/DTO projections?
    

### Relationships

18. Explain `@OneToOne`.
    
19. Explain `@OneToMany`.
    
20. Explain `@ManyToOne`.
    
21. Explain `@ManyToMany`.
    
22. What is `mappedBy`?
    
23. What is the owning side?
    
24. What is `@JoinColumn`?
    
25. What is cascade?
    
26. What is `orphanRemoval`?
    

### Performance

27. LAZY vs EAGER?
    
28. What is the N+1 problem?
    
29. How do you solve N+1?
    
30. What is `JOIN FETCH`?
    
31. What is `EntityGraph`?
    
32. What is first-level cache?
    
33. What is second-level cache?
    

### Transactions/JPA internals

34. What is persistence context?
    
35. What is an entity lifecycle?
    
36. What is dirty checking?
    
37. What is flushing?
    
38. Flush vs commit?
    
39. `save()` vs `saveAndFlush()`?
    
40. `findById()` vs `getReferenceById()`?
    
41. What does `@Transactional` do?
    
42. What is optimistic locking?
    
43. What is `@Version`?
    
44. What is pessimistic locking?
    

## ⭐ The interview flow to memorize

If an interviewer asks **"Explain how Spring Boot JPA works"**, a strong answer is:

```text
Controller
    ↓
Service
    ↓
Repository
    ↓
Spring Data JPA
    ↓
JPA API
    ↓
Hibernate
    ↓
JDBC
    ↓
Database
```

And:

```text
@Entity
   ↓
Entity managed by Persistence Context
   ↓
Repository
   ↓
Hibernate generates SQL
   ↓
JDBC
   ↓
Database
```

The **highest-priority topics for a fresher interview** are: `@Entity`, `@Id`, `JpaRepository`, derived queries, `@Query`, relationships, `mappedBy`, LAZY/EAGER, cascade, persistence context, dirty checking, transactions, N+1, and `JOIN FETCH`.