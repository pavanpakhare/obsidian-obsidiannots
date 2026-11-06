[[DBMS]]
# SQL Complete Tutorial

SQL (**Structured Query Language**) is the language used to **store, retrieve, modify, and manage data in relational databases** such as PostgreSQL, MySQL, Oracle, and SQL Server.

Since you're learning **Java + Spring Boot + PostgreSQL**, I'll focus on standard SQL and PostgreSQL concepts that are useful for backend development.

---

# 1. What is a Database?

A database stores organized data.

For example, an e-commerce application might have:

```text
users
products
orders
order_items
payments
```

A table looks like:

```text
users

id | name  | email              | age
---+-------+--------------------+----
1  | Rahul | rahul@gmail.com    | 22
2  | Amit  | amit@gmail.com     | 25
3  | Priya | priya@gmail.com    | 21
```

### Important terms

|Term|Meaning|
|---|---|
|Database|Collection of data|
|Table|Data organized into rows and columns|
|Row|One record|
|Column|Attribute of a record|
|Primary Key|Uniquely identifies a row|
|Foreign Key|Connects tables|
|SQL|Language used to work with databases|

---

# 2. SQL vs Database

They are not the same thing.

```text
PostgreSQL
    ↓
Database Management System

SQL
    ↓
Language used to communicate with it
```

Examples of database systems:

- PostgreSQL
    
- MySQL
    
- Oracle Database
    
- Microsoft SQL Server
    
- SQLite
    

---

# 3. Create a Database

PostgreSQL:

```sql
CREATE DATABASE ecommerce;
```

Connect to it:

```sql
\c ecommerce
```

`CREATE DATABASE` is SQL, while `\c` is a PostgreSQL `psql` command.

---

# 4. Create a Table

```sql
CREATE TABLE users (
    id BIGINT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(255),
    age INT
);
```

Structure:

```text
users
│
├── id
├── name
├── email
└── age
```

---

# 5. SQL Data Types

Common types:

### Numbers

```sql
INT
BIGINT
DECIMAL(10,2)
NUMERIC
```

Example:

```sql
price DECIMAL(10,2)
```

Can store:

```text
999.99
```

### Strings

```sql
CHAR
VARCHAR
TEXT
```

Example:

```sql
name VARCHAR(100)
description TEXT
```

### Boolean

```sql
is_active BOOLEAN
```

Values:

```sql
TRUE
FALSE
```

### Date/time

```sql
DATE
TIME
TIMESTAMP
TIMESTAMPTZ
```

Example:

```sql
created_at TIMESTAMPTZ
```

---

# 6. INSERT

Insert data:

```sql
INSERT INTO users (id, name, email, age)
VALUES (1, 'Rahul', 'rahul@gmail.com', 22);
```

Multiple rows:

```sql
INSERT INTO users (id, name, email, age)
VALUES
(2, 'Amit', 'amit@gmail.com', 25),
(3, 'Priya', 'priya@gmail.com', 21),
(4, 'John', 'john@gmail.com', 30);
```

---

# 7. SELECT

Retrieve data:

```sql
SELECT * FROM users;
```

`*` means all columns.

Better:

```sql
SELECT id, name, email
FROM users;
```

---

# 8. WHERE

Filter rows.

```sql
SELECT *
FROM users
WHERE age > 22;
```

Example:

```text
Amit | 25
John | 30
```

---

# 9. Comparison Operators

```sql
=
<>
!=
>
<
>=
<=
```

Examples:

```sql
SELECT *
FROM users
WHERE age = 25;
```

```sql
SELECT *
FROM users
WHERE age >= 25;
```

---

# 10. AND / OR / NOT

### AND

Both conditions must be true.

```sql
SELECT *
FROM users
WHERE age > 20
AND age < 30;
```

### OR

```sql
SELECT *
FROM users
WHERE age = 21
OR age = 30;
```

### NOT

```sql
SELECT *
FROM users
WHERE NOT age = 30;
```

---

# 11. IN

Instead of:

```sql
WHERE age = 21
OR age = 25
OR age = 30
```

Use:

```sql
WHERE age IN (21, 25, 30);
```

---

# 12. BETWEEN

```sql
SELECT *
FROM users
WHERE age BETWEEN 20 AND 30;
```

Usually easier to read than:

```sql
WHERE age >= 20 AND age <= 30;
```

---

# 13. LIKE

Search text patterns.

```sql
SELECT *
FROM users
WHERE name LIKE 'R%';
```

Means:

```text
Starts with R
```

Examples:

```sql
LIKE 'R%'
```

Starts with R.

```sql
LIKE '%a'
```

Ends with a.

```sql
LIKE '%rah%'
```

Contains `rah`.

---

# 14. NULL

`NULL` means missing/unknown value.

```sql
SELECT *
FROM users
WHERE email IS NULL;
```

Not:

```sql
WHERE email = NULL
```

Use:

```sql
IS NULL
IS NOT NULL
```

---

# 15. ORDER BY

Sort results.

Ascending:

```sql
SELECT *
FROM users
ORDER BY age ASC;
```

Descending:

```sql
SELECT *
FROM users
ORDER BY age DESC;
```

`ASC` is default.

---

# 16. LIMIT

Get only a certain number of rows.

```sql
SELECT *
FROM users
LIMIT 10;
```

Very common for pagination.

---

# 17. OFFSET

```sql
SELECT *
FROM users
LIMIT 10 OFFSET 20;
```

Meaning:

```text
Skip 20
Take next 10
```

---

# 18. UPDATE

Modify existing data.

```sql
UPDATE users
SET age = 23
WHERE id = 1;
```

⚠️ Be careful:

```sql
UPDATE users
SET age = 23;
```

Without `WHERE`, **every user is updated**.

---

# 19. DELETE

```sql
DELETE FROM users
WHERE id = 1;
```

Again:

```sql
DELETE FROM users;
```

deletes **all rows**.

---

# 20. Primary Key

A primary key uniquely identifies a row.

```sql
CREATE TABLE users (
    id BIGINT PRIMARY KEY,
    name VARCHAR(100)
);
```

You can't have:

```text
id
1
1
```

because IDs must be unique.

---

# 21. Auto-generated IDs

PostgreSQL:

```sql
CREATE TABLE users (
    id BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(255)
);
```

Then:

```sql
INSERT INTO users (name, email)
VALUES ('Rahul', 'rahul@gmail.com');
```

PostgreSQL automatically generates the ID.

---

# 22. Constraints

Constraints enforce rules on data.

Common constraints:

```text
PRIMARY KEY
FOREIGN KEY
NOT NULL
UNIQUE
CHECK
DEFAULT
```

Example:

```sql
CREATE TABLE users (
    id BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,

    name VARCHAR(100) NOT NULL,

    email VARCHAR(255) UNIQUE,

    age INT CHECK (age >= 18),

    active BOOLEAN DEFAULT TRUE
);
```

---

# 23. NOT NULL

Prevents missing values.

```sql
name VARCHAR(100) NOT NULL
```

This is invalid:

```sql
INSERT INTO users (email)
VALUES ('test@gmail.com');
```

because `name` is required.

---

# 24. UNIQUE

```sql
email VARCHAR(255) UNIQUE
```

Prevents duplicate emails.

```text
rahul@gmail.com
rahul@gmail.com  ❌
```

---

# 25. DEFAULT

```sql
active BOOLEAN DEFAULT TRUE
```

If you don't provide `active`:

```sql
INSERT INTO users (name)
VALUES ('Rahul');
```

the database automatically uses:

```text
active = true
```

---

# 26. CHECK

```sql
age INT CHECK (age >= 18)
```

Prevents:

```text
age = 10
```

---

# 27. Foreign Keys

Suppose we have:

```text
users
orders
```

One user can have many orders.

```sql
CREATE TABLE orders (
    id BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    user_id BIGINT NOT NULL,

    FOREIGN KEY (user_id)
        REFERENCES users(id)
);
```

Relationship:

```text
users
  │
  │ 1
  │
  └──────────< orders
                many
```

---

# 28. Relationships

Three important relationships:

### One-to-One

```text
User ─── Profile
```

### One-to-Many

```text
User ───< Orders
```

### Many-to-Many

```text
Students >───< Courses
```

Many-to-many normally requires a junction table.

---

# 29. Many-to-Many

```sql
CREATE TABLE students (
    id BIGINT PRIMARY KEY,
    name VARCHAR(100)
);

CREATE TABLE courses (
    id BIGINT PRIMARY KEY,
    name VARCHAR(100)
);
```

Junction table:

```sql
CREATE TABLE student_courses (
    student_id BIGINT REFERENCES students(id),
    course_id BIGINT REFERENCES courses(id),

    PRIMARY KEY (student_id, course_id)
);
```

---

# 30. JOIN

JOIN combines data from multiple tables.

Suppose:

```text
users

id | name
1  | Rahul
2  | Amit
```

and:

```text
orders

id | user_id | amount
1  | 1       | 500
2  | 1       | 700
3  | 2       | 300
```

Query:

```sql
SELECT
    users.name,
    orders.amount
FROM users
JOIN orders
    ON users.id = orders.user_id;
```

Result:

```text
Rahul | 500
Rahul | 700
Amit  | 300
```

---

# 31. INNER JOIN

Only matching rows.

```sql
SELECT *
FROM users
INNER JOIN orders
ON users.id = orders.user_id;
```

---

# 32. LEFT JOIN

Returns **all rows from the left table**, even without a match.

```sql
SELECT
    users.name,
    orders.amount
FROM users
LEFT JOIN orders
ON users.id = orders.user_id;
```

Useful for:

> Find all users, including users who haven't placed an order.

---

# 33. RIGHT JOIN

Opposite of LEFT JOIN:

```sql
SELECT *
FROM users
RIGHT JOIN orders
ON users.id = orders.user_id;
```

Less commonly used because you can usually rewrite it as a LEFT JOIN.

---

# 34. FULL OUTER JOIN

Returns matching and non-matching rows from both tables.

```sql
SELECT *
FROM users
FULL OUTER JOIN orders
ON users.id = orders.user_id;
```

---

# 35. SQL Aggregation

SQL can calculate:

```text
COUNT
SUM
AVG
MIN
MAX
```

### COUNT

```sql
SELECT COUNT(*)
FROM users;
```

### SUM

```sql
SELECT SUM(amount)
FROM orders;
```

### AVG

```sql
SELECT AVG(amount)
FROM orders;
```

### MIN

```sql
SELECT MIN(amount)
FROM orders;
```

### MAX

```sql
SELECT MAX(amount)
FROM orders;
```

---

# 36. GROUP BY

Suppose:

```text
orders

user_id | amount
--------+-------
1       | 500
1       | 700
2       | 300
2       | 400
```

Find total spending per user:

```sql
SELECT
    user_id,
    SUM(amount) AS total
FROM orders
GROUP BY user_id;
```

Result:

```text
user_id | total
--------+------
1       | 1200
2       | 700
```

---

# 37. HAVING

`WHERE` filters rows.

`HAVING` filters groups.

```sql
SELECT
    user_id,
    SUM(amount) AS total
FROM orders
GROUP BY user_id
HAVING SUM(amount) > 1000;
```

---

# 38. WHERE vs HAVING

```text
WHERE
 ↓
Filter individual rows

GROUP BY
 ↓
Create groups

HAVING
 ↓
Filter groups
```

Example:

```sql
SELECT user_id, SUM(amount)
FROM orders
WHERE amount > 100
GROUP BY user_id
HAVING SUM(amount) > 1000;
```

---

# 39. DISTINCT

Remove duplicates.

```sql
SELECT DISTINCT age
FROM users;
```

Example:

```text
21
22
25
30
```

---

# 40. Aliases

Rename columns temporarily.

```sql
SELECT
    name AS username,
    email AS user_email
FROM users;
```

Table alias:

```sql
SELECT u.name
FROM users AS u;
```

This is extremely common with JOINs.

---

# 41. Subqueries

A query inside another query.

Example:

```sql
SELECT *
FROM users
WHERE id IN (
    SELECT user_id
    FROM orders
);
```

Meaning:

> Find users who have placed orders.

---

# 42. EXISTS

Another way:

```sql
SELECT *
FROM users u
WHERE EXISTS (
    SELECT 1
    FROM orders o
    WHERE o.user_id = u.id
);
```

`EXISTS` checks whether matching rows exist.

---

# 43. CASE

SQL's conditional expression.

```sql
SELECT
    name,
    age,
    CASE
        WHEN age < 18 THEN 'Minor'
        WHEN age < 60 THEN 'Adult'
        ELSE 'Senior'
    END AS category
FROM users;
```

---

# 44. COALESCE

Returns the first non-null value.

```sql
SELECT COALESCE(email, 'No Email')
FROM users;
```

If email is NULL:

```text
No Email
```

---

# 45. String Functions

Examples:

```sql
UPPER(name)
LOWER(name)
LENGTH(name)
TRIM(name)
CONCAT(first_name, ' ', last_name)
```

Example:

```sql
SELECT UPPER(name)
FROM users;
```

---

# 46. Date Functions

Examples:

```sql
CURRENT_DATE
CURRENT_TIMESTAMP
```

Example:

```sql
SELECT CURRENT_DATE;
```

Filter recent records:

```sql
SELECT *
FROM orders
WHERE created_at >= CURRENT_DATE - INTERVAL '7 days';
```

---

# 47. ALTER TABLE

Modify table structure.

Add column:

```sql
ALTER TABLE users
ADD COLUMN phone VARCHAR(20);
```

Remove column:

```sql
ALTER TABLE users
DROP COLUMN phone;
```

Rename column:

```sql
ALTER TABLE users
RENAME COLUMN name TO full_name;
```

---

# 48. DROP vs DELETE vs TRUNCATE

Very important.

### DELETE

```sql
DELETE FROM users
WHERE id = 5;
```

Deletes rows.

### TRUNCATE

```sql
TRUNCATE TABLE users;
```

Removes all rows efficiently.

### DROP

```sql
DROP TABLE users;
```

Removes the **entire table structure and data**.

Think:

```text
DELETE
   ↓
Remove selected/all rows

TRUNCATE
   ↓
Remove all rows

DROP
   ↓
Remove table itself
```

---

# 49. Transactions

A transaction groups operations into one unit.

Example:

```sql
BEGIN;

UPDATE accounts
SET balance = balance - 100
WHERE id = 1;

UPDATE accounts
SET balance = balance + 100
WHERE id = 2;

COMMIT;
```

If something goes wrong:

```sql
ROLLBACK;
```

---

# 50. ACID

Database transactions generally follow ACID properties.

### Atomicity

All operations happen or none happen.

```text
Transfer ₹100

Debit ✔
Credit ❌

→ Rollback
```

### Consistency

Database remains valid.

### Isolation

Concurrent transactions shouldn't improperly interfere with each other.

### Durability

Committed data survives failures.

---

# 51. Indexes

Indexes make searching faster.

```sql
CREATE INDEX idx_users_email
ON users(email);
```

Then:

```sql
SELECT *
FROM users
WHERE email = 'rahul@gmail.com';
```

can be much faster on a large table.

But indexes have a cost:

```text
Faster SELECT
       ↓
More storage
       +
Slower INSERT/UPDATE/DELETE
```

Don't blindly index every column.

---

# 52. Composite Index

Index multiple columns:

```sql
CREATE INDEX idx_users_name_age
ON users(name, age);
```

Useful when queries commonly filter/sort by those columns together.

The **column order matters**.

---

# 53. EXPLAIN

Check how the database executes a query.

```sql
EXPLAIN
SELECT *
FROM users
WHERE email = 'rahul@gmail.com';
```

PostgreSQL also supports:

```sql
EXPLAIN ANALYZE
SELECT *
FROM users
WHERE email = 'rahul@gmail.com';
```

`EXPLAIN ANALYZE` actually executes the query and reports runtime information.

This is important for database performance tuning.

---

# 54. Views

A view is a stored query that behaves like a virtual table.

```sql
CREATE VIEW user_orders AS
SELECT
    u.name,
    o.amount
FROM users u
JOIN orders o
ON u.id = o.user_id;
```

Then:

```sql
SELECT *
FROM user_orders;
```

---

# 55. CTE

Common Table Expression.

```sql
WITH high_value_orders AS (
    SELECT *
    FROM orders
    WHERE amount > 1000
)
SELECT *
FROM high_value_orders;
```

CTEs make complex queries easier to organize.

---

# 56. Window Functions

Very useful for advanced SQL.

Example:

```sql
SELECT
    name,
    salary,
    RANK() OVER (ORDER BY salary DESC) AS rank
FROM employees;
```

Result:

```text
name   salary   rank
Amit   100000   1
Rahul   90000   2
John    80000   3
```

Other window functions:

```text
ROW_NUMBER()
RANK()
DENSE_RANK()
LAG()
LEAD()
SUM() OVER()
AVG() OVER()
```

---

# 57. Normalization

Normalization organizes data to reduce duplication.

Bad design:

```text
orders

id | user_name | user_email | product
```

Better:

```text
users
-----
id
name
email

orders
------
id
user_id

products
--------
id
name
price
```

Relationships:

```text
users
  │
  ↓
orders
  │
  ↓
products
```

Important normal forms:

```text
1NF
2NF
3NF
BCNF
```

For most application development, understanding **1NF–3NF** is a good starting point.

---

# 58. SQL Command Categories

A useful way to remember SQL:

### DDL — Data Definition Language

Defines database structure.

```sql
CREATE
ALTER
DROP
TRUNCATE
```

### DML — Data Manipulation Language

Changes data.

```sql
INSERT
UPDATE
DELETE
```

### DQL — Data Query Language

Retrieves data.

```sql
SELECT
```

### DCL — Data Control Language

Permissions.

```sql
GRANT
REVOKE
```

### TCL — Transaction Control Language

Transactions.

```sql
BEGIN
COMMIT
ROLLBACK
SAVEPOINT
```

---

# 59. SQL Query Execution Order

This is **very important** for understanding complex SQL.

When you write:

```sql
SELECT
    department,
    AVG(salary)
FROM employees
WHERE age > 20
GROUP BY department
HAVING AVG(salary) > 50000
ORDER BY AVG(salary) DESC
LIMIT 5;
```

Conceptually, SQL processes it roughly as:

```text
FROM
 ↓
WHERE
 ↓
GROUP BY
 ↓
HAVING
 ↓
SELECT
 ↓
ORDER BY
 ↓
LIMIT
```

This explains many SQL behaviors that initially seem confusing.

---

# 60. Real E-Commerce Example

Let's create a simplified schema.

```sql
CREATE TABLE users (
    id BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL
);
```

Products:

```sql
CREATE TABLE products (
    id BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    name VARCHAR(200) NOT NULL,
    price DECIMAL(10,2) NOT NULL,
    stock INT NOT NULL DEFAULT 0
);
```

Orders:

```sql
CREATE TABLE orders (
    id BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    user_id BIGINT NOT NULL,
    created_at TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP,

    FOREIGN KEY (user_id)
        REFERENCES users(id)
);
```

Order items:

```sql
CREATE TABLE order_items (
    id BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,

    order_id BIGINT NOT NULL,
    product_id BIGINT NOT NULL,

    quantity INT NOT NULL,
    price DECIMAL(10,2) NOT NULL,

    FOREIGN KEY (order_id)
        REFERENCES orders(id),

    FOREIGN KEY (product_id)
        REFERENCES products(id)
);
```

Relationship:

```text
             ┌───────────┐
             │   users   │
             └─────┬─────┘
                   │
                   │ 1:N
                   ↓
             ┌───────────┐
             │  orders   │
             └─────┬─────┘
                   │
                   │ 1:N
                   ↓
          ┌────────────────┐
          │  order_items   │
          └───────┬────────┘
                  │
                  │ N:1
                  ↓
             ┌───────────┐
             │ products  │
             └───────────┘
```

---

# 61. Find a User's Orders

```sql
SELECT
    o.id,
    o.created_at
FROM orders o
WHERE o.user_id = 1;
```

---

# 62. Find Order Details

```sql
SELECT
    o.id AS order_id,
    p.name AS product,
    oi.quantity,
    oi.price
FROM orders o
JOIN order_items oi
    ON o.id = oi.order_id
JOIN products p
    ON p.id = oi.product_id
WHERE o.id = 10;
```

---

# 63. Calculate Order Total

```sql
SELECT
    SUM(quantity * price) AS total
FROM order_items
WHERE order_id = 10;
```

---

# 64. Find Top Products

```sql
SELECT
    p.name,
    SUM(oi.quantity) AS total_sold
FROM products p
JOIN order_items oi
    ON p.id = oi.product_id
GROUP BY p.id, p.name
ORDER BY total_sold DESC
LIMIT 10;
```

This combines:

```text
JOIN
GROUP BY
SUM
ORDER BY
LIMIT
```

These are exactly the kinds of queries you encounter in backend applications.

---

# 65. SQL with Spring Boot

Your architecture will typically look like:

```text
React
  ↓
HTTP
  ↓
Spring Boot
  ↓
Spring Data JPA
  ↓
Hibernate
  ↓
JDBC
  ↓
PostgreSQL
```

For example, Java:

```java
public interface UserRepository
        extends JpaRepository<User, Long> {

    Optional<User> findByEmail(String email);
}
```

Spring Data may generate SQL similar to:

```sql
SELECT *
FROM users
WHERE email = ?;
```

For custom queries:

```java
@Query("""
    SELECT u
    FROM User u
    WHERE u.email = :email
""")
Optional<User> findByEmail(String email);
```

Or native SQL:

```java
@Query(
    value = "SELECT * FROM users WHERE email = :email",
    nativeQuery = true
)
Optional<User> findByEmail(@Param("email") String email);
```

---

# 66. SQL Injection

Never construct SQL like this:

```java
String sql =
    "SELECT * FROM users WHERE email = '" + email + "'";
```

An attacker could manipulate the input.

Use parameterized queries:

```sql
SELECT *
FROM users
WHERE email = ?;
```

JDBC:

```java
PreparedStatement statement =
    connection.prepareStatement(
        "SELECT * FROM users WHERE email = ?"
    );

statement.setString(1, email);
```

Spring Data/JPA and `JdbcTemplate` can handle parameter binding for you.

---

# 67. What SQL Should a Java Developer Know?

For a **Java/Spring Boot backend developer**, prioritize:

### Beginner

```text
CREATE TABLE
INSERT
SELECT
WHERE
UPDATE
DELETE
ORDER BY
LIMIT
DISTINCT
NULL
```

### Intermediate

```text
JOIN
INNER JOIN
LEFT JOIN
GROUP BY
HAVING
COUNT
SUM
AVG
SUBQUERY
CASE
COALESCE
```

### Database design

```text
Primary Keys
Foreign Keys
Constraints
Normalization
Relationships
Indexes
Composite indexes
```

### Advanced

```text
Transactions
ACID
Isolation levels
CTEs
Window functions
EXPLAIN
Query optimization
Locks
Deadlocks
MVCC
Partitioning
```

### PostgreSQL

Since you're using PostgreSQL with Spring Boot, also learn:

```text
SERIAL / IDENTITY
JSONB
ARRAY
UUID
TIMESTAMPTZ
ILIKE
RETURNING
ON CONFLICT
PostgreSQL indexes
EXPLAIN ANALYZE
```

---

# 68. SQL Learning Roadmap

I'd recommend learning SQL in this order:

```text
                    SQL
                     │
        ┌────────────┴────────────┐
        ↓                         ↓
    Basics                    Database Design
        │                         │
 SELECT / INSERT             PK / FK
 UPDATE / DELETE             Relationships
 WHERE                       Constraints
 ORDER BY                    Normalization
        │
        ↓
    Intermediate
        │
 JOIN
 GROUP BY
 HAVING
 Subqueries
 Aggregate functions
        │
        ↓
     Advanced
        │
 Transactions
 Indexes
 CTEs
 Window Functions
 EXPLAIN
 Query Optimization
        │
        ↓
    PostgreSQL
        │
 JSONB
 UUID
 RETURNING
 ON CONFLICT
 MVCC
 PostgreSQL indexes
        │
        ↓
 Spring Boot + JPA
        │
 Hibernate
 Spring Data JPA
 JDBC
 Transactions
```

**For your Java/Spring Boot path, SQL + PostgreSQL + JPA/Hibernate should be learned together**, because knowing SQL alone isn't enough—you also need to understand how Hibernate turns your Java entity/repository operations into database queries.