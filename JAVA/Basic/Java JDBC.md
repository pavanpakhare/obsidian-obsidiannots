[[java]]

# JDBC Tutorial for Java Interviews

**JDBC (Java Database Connectivity)** is a Java API used to connect Java applications with relational databases such as PostgreSQL, MySQL, and Oracle.

For interviews, focus on **architecture, steps, important interfaces, CRUD, transactions, PreparedStatement, ResultSet, and common questions**.

---

## 1. JDBC Architecture

```text
Java Application
       |
       v
    JDBC API
       |
       v
 JDBC Driver
       |
       v
   Database
(PostgreSQL/MySQL)
```

JDBC provides a standard API, while the **JDBC driver** translates JDBC calls into database-specific communication.

---

# 2. Important JDBC Interfaces

|Interface/Class|Purpose|
|---|---|
|`DriverManager`|Creates database connections|
|`Connection`|Represents connection to DB|
|`Statement`|Executes simple SQL|
|`PreparedStatement`|Executes parameterized SQL|
|`CallableStatement`|Calls stored procedures|
|`ResultSet`|Holds SELECT query results|
|`SQLException`|Handles database-related errors|
|`Savepoint`|Creates transaction savepoints|

---

# 3. JDBC Steps

The standard flow is:

```text
1. Load/register driver
        ↓
2. Get Connection
        ↓
3. Create Statement/PreparedStatement
        ↓
4. Execute SQL
        ↓
5. Process ResultSet
        ↓
6. Close resources
```

Modern JDBC drivers are generally discovered automatically through the JDBC 4+ service-provider mechanism, so explicitly calling `Class.forName()` is usually unnecessary.

---

# 4. Add JDBC Driver

For Maven + PostgreSQL:

```xml
<dependency>
    <groupId>org.postgresql</groupId>
    <artifactId>postgresql</artifactId>
    <version>YOUR_VERSION</version>
</dependency>
```

For MySQL:

```xml
<dependency>
    <groupId>com.mysql</groupId>
    <artifactId>mysql-connector-j</artifactId>
    <version>YOUR_VERSION</version>
</dependency>
```

---

# 5. Create Database Connection

PostgreSQL example:

```java
String url = "jdbc:postgresql://localhost:5432/testdb";
String username = "postgres";
String password = "password";

Connection connection =
        DriverManager.getConnection(url, username, password);
```

### Interview question

**What does `DriverManager.getConnection()` do?**

It establishes a connection between the Java application and the database using the appropriate JDBC driver.

---

# 6. Statement

`Statement` is used to execute static SQL.

```java
Connection connection =
        DriverManager.getConnection(url, username, password);

Statement statement = connection.createStatement();

ResultSet rs = statement.executeQuery(
        "SELECT id, name FROM users"
);

while (rs.next()) {
    int id = rs.getInt("id");
    String name = rs.getString("name");

    System.out.println(id + " " + name);
}
```

---

# 7. ResultSet

`ResultSet` represents the result returned by a `SELECT` query.

```java
while (rs.next()) {
    System.out.println(rs.getInt("id"));
    System.out.println(rs.getString("name"));
}
```

Initially, the cursor is positioned **before the first row**.

Calling:

```java
rs.next();
```

moves it to the next row.

---

# 8. `executeQuery()` vs `executeUpdate()` vs `execute()`

### `executeQuery()`

Used mainly for `SELECT`.

```java
ResultSet rs = statement.executeQuery(
    "SELECT * FROM users"
);
```

Returns:

```text
ResultSet
```

### `executeUpdate()`

Used for:

```text
INSERT
UPDATE
DELETE
```

Example:

```java
int rows = statement.executeUpdate(
    "UPDATE users SET name='Pavan' WHERE id=1"
);

System.out.println(rows);
```

Returns the number of affected rows.

### `execute()`

Can execute SQL that may return either a result set or update count.

```java
boolean result = statement.execute(sql);
```

---

# 9. PreparedStatement ⭐

This is **very important for interviews**.

Instead of:

```java
String sql =
    "SELECT * FROM users WHERE email = '" + email + "'";
```

use:

```java
String sql =
    "SELECT * FROM users WHERE email = ?";

PreparedStatement ps =
    connection.prepareStatement(sql);

ps.setString(1, email);

ResultSet rs = ps.executeQuery();
```

### Why PreparedStatement?

1. Prevents SQL injection.
    
2. Supports parameters.
    
3. More convenient for repeated queries.
    
4. Database can potentially reuse/prepare execution plans depending on the driver/database.
    

---

# 10. PreparedStatement Parameters

```java
String sql = """
    INSERT INTO users(name, email, age)
    VALUES (?, ?, ?)
    """;

PreparedStatement ps =
    connection.prepareStatement(sql);

ps.setString(1, "Pavan");
ps.setString(2, "pavan@example.com");
ps.setInt(3, 22);

int rows = ps.executeUpdate();
```

Parameter numbering starts from **1**, not 0.

```java
ps.setString(1, ...);
ps.setInt(2, ...);
```

---

# 11. CRUD Using JDBC

### CREATE

```java
String sql =
    "INSERT INTO users(name, email) VALUES (?, ?)";

PreparedStatement ps =
    connection.prepareStatement(sql);

ps.setString(1, "Pavan");
ps.setString(2, "pavan@example.com");

ps.executeUpdate();
```

### READ

```java
String sql =
    "SELECT id, name, email FROM users";

PreparedStatement ps =
    connection.prepareStatement(sql);

ResultSet rs = ps.executeQuery();

while (rs.next()) {
    System.out.println(
        rs.getInt("id") + " " +
        rs.getString("name") + " " +
        rs.getString("email")
    );
}
```

### UPDATE

```java
String sql =
    "UPDATE users SET email=? WHERE id=?";

PreparedStatement ps =
    connection.prepareStatement(sql);

ps.setString(1, "new@example.com");
ps.setInt(2, 1);

ps.executeUpdate();
```

### DELETE

```java
String sql =
    "DELETE FROM users WHERE id=?";

PreparedStatement ps =
    connection.prepareStatement(sql);

ps.setInt(1, 1);

ps.executeUpdate();
```

---

# 12. Try-with-Resources ⭐

Instead of manually closing everything:

```java
rs.close();
ps.close();
connection.close();
```

use:

```java
String sql = "SELECT * FROM users";

try (
    Connection con = DriverManager.getConnection(
        url, username, password
    );
    PreparedStatement ps = con.prepareStatement(sql);
    ResultSet rs = ps.executeQuery()
) {
    while (rs.next()) {
        System.out.println(
            rs.getInt("id") + " " +
            rs.getString("name")
        );
    }
}
```

Resources implementing `AutoCloseable` are automatically closed.

---

# 13. JDBC Transactions ⭐⭐⭐

By default, a JDBC connection normally operates with **auto-commit enabled**.

```java
connection.setAutoCommit(false);
```

Now multiple operations can be treated as one transaction.

```java
try {
    connection.setAutoCommit(false);

    // operation 1
    PreparedStatement ps1 =
        connection.prepareStatement(
            "UPDATE accounts SET balance = balance - 100 WHERE id = ?"
        );

    ps1.setInt(1, 1);
    ps1.executeUpdate();

    // operation 2
    PreparedStatement ps2 =
        connection.prepareStatement(
            "UPDATE accounts SET balance = balance + 100 WHERE id = ?"
        );

    ps2.setInt(1, 2);
    ps2.executeUpdate();

    connection.commit();

} catch (SQLException e) {

    connection.rollback();

}
```

### Important methods

```java
setAutoCommit(false)
commit()
rollback()
```

---

# 14. Savepoint

A savepoint allows partial rollback.

```java
connection.setAutoCommit(false);

Savepoint savepoint =
    connection.setSavepoint();

try {
    // some operations

    connection.rollback(savepoint);

} catch (SQLException e) {
    connection.rollback();
}
```

---

# 15. CallableStatement

Used to call stored procedures.

```java
CallableStatement cs =
    connection.prepareCall("{call get_users()}");

ResultSet rs = cs.executeQuery();
```

For parameters:

```java
CallableStatement cs =
    connection.prepareCall("{call get_user(?)}");

cs.setInt(1, 10);

ResultSet rs = cs.executeQuery();
```

---

# 16. Batch Processing ⭐

Useful when executing many similar operations.

```java
String sql =
    "INSERT INTO users(name) VALUES (?)";

PreparedStatement ps =
    connection.prepareStatement(sql);

ps.setString(1, "A");
ps.addBatch();

ps.setString(1, "B");
ps.addBatch();

ps.setString(1, "C");
ps.addBatch();

int[] results = ps.executeBatch();
```

Instead of sending every statement individually, batching can reduce database/network overhead.

---

# 17. Connection Pooling

Creating a database connection can be expensive.

Instead of:

```text
Request
  ↓
Create connection
  ↓
Query
  ↓
Close connection
```

a connection pool maintains reusable connections:

```text
          Connection Pool
       ┌─────┬─────┬─────┐
       │ C1  │ C2  │ C3  │
       └─────┴─────┴─────┘
          ↑
          |
      Application
```

Common pooling technology:

**HikariCP**

Spring Boot commonly uses HikariCP by default when JDBC/JPA starters are used.

---

# 18. JDBC vs JPA vs Hibernate

This is a common interview question.

|JDBC|JPA|Hibernate|
|---|---|---|
|Java DB API|Specification|JPA implementation|
|SQL-centric|Entity/object-centric|ORM|
|More boilerplate|Less boilerplate|Less boilerplate|
|Manual mapping|Entity mapping|Entity mapping|
|Direct DB interaction|Abstraction|ORM framework|

Example JDBC:

```java
SELECT * FROM users WHERE id = ?
```

With JPA:

```java
User user = entityManager.find(User.class, id);
```

---

# 19. Statement vs PreparedStatement

|Statement|PreparedStatement|
|---|---|
|Static SQL|Parameterized SQL|
|Parameters harder to handle|Supports `?` parameters|
|More SQL-injection risk when concatenating input|Helps prevent SQL injection|
|Suitable for simple static SQL|Preferred for user-provided values|

### Interview answer

> `PreparedStatement` is generally preferred because it supports parameterized queries and avoids constructing SQL by concatenating untrusted input, which helps prevent SQL injection.

---

# 20. Important JDBC Interfaces Hierarchy

A simplified view:

```text
                JDBC
                 |
        ┌────────┴─────────┐
        |                  |
   DriverManager       DataSource
        |
    Connection
        |
   ┌────┼───────────────┐
   |    |               |
Statement PreparedStatement CallableStatement
             |
         ResultSet
```

Note: `PreparedStatement` extends `Statement`, and `CallableStatement` extends `PreparedStatement`.

---

# 21. SQLException

Database operations can throw:

```java
SQLException
```

Example:

```java
try {
    Connection con =
        DriverManager.getConnection(url, user, password);
}
catch (SQLException e) {
    System.out.println(e.getMessage());
}
```

Useful methods:

```java
e.getMessage();
e.getSQLState();
e.getErrorCode();
```

---

# 22. Primary JDBC Interview Questions

### Beginner

**1. What is JDBC?**

Java API for interacting with relational databases.

**2. What is a JDBC driver?**

Software that allows JDBC to communicate with a particular database.

**3. What are the main JDBC steps?**

```text
Get connection
→ Create statement
→ Execute query
→ Process ResultSet
→ Close resources
```

**4. What is Connection?**

Represents a session/connection between Java application and database.

**5. What is ResultSet?**

Object containing rows returned by a query.

---

### Intermediate

**6. Statement vs PreparedStatement?**

PreparedStatement supports parameterized SQL and is generally safer for user input.

**7. `executeQuery()` vs `executeUpdate()`?**

```text
executeQuery()  → SELECT → ResultSet
executeUpdate() → INSERT/UPDATE/DELETE → affected row count
```

**8. What is auto-commit?**

When enabled, each individual SQL statement is committed automatically.

```java
connection.setAutoCommit(false);
```

disables it.

**9. How do you perform rollback?**

```java
connection.rollback();
```

**10. Why use try-with-resources?**

It automatically closes JDBC resources.

---

# 23. Advanced Interview Questions

### Q1. What happens when `Connection.close()` is called?

The JDBC connection is closed. With a connection pool, `close()` typically returns the connection to the pool rather than physically closing the underlying database connection.

---

### Q2. Why shouldn't we create a new connection for every operation?

Because establishing connections has overhead and too many connections can exhaust database resources.

Use **connection pooling** for production applications.

---

### Q3. What is SQL injection?

Suppose you construct SQL like:

```java
String sql =
    "SELECT * FROM users WHERE name='" + name + "'";
```

Untrusted input can alter the SQL statement.

Prefer:

```java
PreparedStatement ps =
    connection.prepareStatement(
        "SELECT * FROM users WHERE name=?"
    );

ps.setString(1, name);
```

---

### Q4. What is connection pooling?

A pool maintains reusable database connections so applications don't repeatedly create physical connections.

---

### Q5. What is a transaction?

A transaction groups database operations into a logical unit.

For example:

```text
Transfer ₹100

Account A - ₹100
Account B + ₹100
```

Both should succeed or the transaction should be rolled back.

---

### Q6. What is the difference between `commit()` and `rollback()`?

```text
commit()   → permanently apply transaction changes
rollback() → undo uncommitted transaction changes
```

---

# 24. Most Important Things to Memorize

For a **Java/Spring Boot interview**, make sure you can explain these without looking at notes:

```text
JDBC
 ↓
Driver
 ↓
Connection
 ↓
Statement / PreparedStatement
 ↓
executeQuery / executeUpdate
 ↓
ResultSet
 ↓
Transaction
 ↓
commit / rollback
 ↓
Connection Pool
```

And especially know:

- `Connection`
    
- `Statement`
    
- `PreparedStatement`
    
- `CallableStatement`
    
- `ResultSet`
    
- `SQLException`
    
- `DriverManager`
    
- `DataSource`
    
- `executeQuery()`
    
- `executeUpdate()`
    
- `execute()`
    
- `setAutoCommit()`
    
- `commit()`
    
- `rollback()`
    
- `Savepoint`
    
- Batch processing
    
- Connection pooling
    
- SQL injection
    
- JDBC vs JPA/Hibernate
    

