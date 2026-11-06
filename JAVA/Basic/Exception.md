[[java]]
# Java Exception Handling Tutorial

Exception handling in Java is a mechanism for **handling runtime problems without abruptly terminating the program**.

Example:

```java
int a = 10;
int b = 0;

int result = a / b; // ArithmeticException
```

Without handling, the program terminates with an exception.

---

## 1. What is an Exception?

An **exception** is an abnormal event that occurs during program execution and disrupts the normal flow.

```java
int[] numbers = {10, 20, 30};

System.out.println(numbers[5]);
```

This produces:

```text
ArrayIndexOutOfBoundsException
```

### Common exceptions

|Exception|Example|
|---|---|
|`ArithmeticException`|`10 / 0`|
|`NullPointerException`|Calling method on `null`|
|`ArrayIndexOutOfBoundsException`|Invalid array index|
|`NumberFormatException`|`"abc"` → `Integer.parseInt()`|
|`ClassCastException`|Invalid object casting|
|`IllegalArgumentException`|Invalid method argument|
|`IOException`|File/network I/O failure|
|`SQLException`|Database operation failure|

---

# 2. Exception Hierarchy

The important hierarchy is:

```text
Object
  └── Throwable
       ├── Error
       │    ├── OutOfMemoryError
       │    └── StackOverflowError
       │
       └── Exception
            ├── RuntimeException
            │    ├── NullPointerException
            │    ├── ArithmeticException
            │    └── ...
            │
            └── Other checked exceptions
                 ├── IOException
                 ├── SQLException
                 └── ...
```

### `Throwable`

`Throwable` is the root class for things that can be thrown and caught.

It has two major categories:

- `Error`
    
- `Exception`
    

---

# 3. Error vs Exception

### Error

Usually represents serious JVM/system-level problems.

```java
OutOfMemoryError
StackOverflowError
```

Normally, applications **don't try to recover from Errors**.

### Exception

Represents conditions an application can potentially handle.

```java
IOException
SQLException
NullPointerException
```

---

# 4. Checked vs Unchecked Exceptions

This is extremely important for interviews.

## Checked Exception

Checked by the compiler.

Examples:

```java
IOException
SQLException
FileNotFoundException
```

You must either:

```java
try {
    // code
} catch (IOException e) {
}
```

or declare:

```java
void readFile() throws IOException {
}
```

---

## Unchecked Exception

Subclasses of `RuntimeException`.

Examples:

```java
NullPointerException
ArithmeticException
IllegalArgumentException
ArrayIndexOutOfBoundsException
```

Compiler doesn't force you to handle them.

```java
int x = 10 / 0;
```

The code can compile, but throws an exception at runtime.

### Interview shortcut

```text
Checked Exception
    ↓
Exception
    ↓
NOT RuntimeException

Unchecked Exception
    ↓
RuntimeException
```

---

# 5. `try-catch`

Use `try` for code that may throw an exception.

```java
try {
    int result = 10 / 0;
} catch (ArithmeticException e) {
    System.out.println("Cannot divide by zero");
}
```

Output:

```text
Cannot divide by zero
```

The program can continue after the `catch`.

---

# 6. What happens internally?

Consider:

```java
try {
    int result = 10 / 0;
    System.out.println("Hello");
} catch (ArithmeticException e) {
    System.out.println("Exception occurred");
}

System.out.println("Program continues");
```

Flow:

```text
try
 ↓
10 / 0
 ↓
ArithmeticException
 ↓
Skip remaining try statements
 ↓
catch
 ↓
Program continues
```

So:

```java
System.out.println("Hello");
```

is **not executed**.

---

# 7. Multiple catch Blocks

You can handle different exceptions differently.

```java
try {
    int[] arr = {1, 2, 3};

    System.out.println(arr[5]);

} catch (ArithmeticException e) {
    System.out.println("Arithmetic problem");

} catch (ArrayIndexOutOfBoundsException e) {
    System.out.println("Invalid array index");
}
```

### Important rule

More specific exceptions must come before broader exceptions.

Correct:

```java
catch (ArithmeticException e) {
}
catch (RuntimeException e) {
}
```

Incorrect:

```java
catch (RuntimeException e) {
}
catch (ArithmeticException e) {
}
```

Because `ArithmeticException` is already covered by `RuntimeException`.

---

# 8. Multi-Catch

Java allows multiple exception types in one catch.

```java
try {
    // code
} catch (ArithmeticException | NullPointerException e) {
    System.out.println("Exception occurred");
}
```

Useful when handling different exceptions in the **same way**.

---

# 9. `finally`

`finally` normally executes whether an exception occurs or not.

```java
try {
    System.out.println("Try");
} catch (Exception e) {
    System.out.println("Catch");
} finally {
    System.out.println("Finally");
}
```

Output:

```text
Try
Finally
```

With an exception:

```text
Try
Catch
Finally
```

### Common use

Resource cleanup:

```java
try {
    // use resource
} finally {
    // cleanup
}
```

Although for modern Java, **try-with-resources** is usually preferred for closeable resources.

---

# 10. `throw`

`throw` is used to **explicitly throw an exception**.

```java
int age = 15;

if (age < 18) {
    throw new IllegalArgumentException("Age must be 18 or above");
}
```

Syntax:

```java
throw new ExceptionType("message");
```

Example:

```java
throw new RuntimeException("Something went wrong");
```

---

# 11. `throws`

`throws` is used in a method declaration to indicate that a method may throw exceptions.

```java
void readFile() throws IOException {
    // file operation
}
```

The caller then has to handle or further declare the exception.

```java
void process() throws IOException {
    readFile();
}
```

---

# 12. `throw` vs `throws`

Very common interview question.

|`throw`|`throws`|
|---|---|
|Actually throws an exception|Declares possible exceptions|
|Used inside method/body|Used in method signature|
|Throws one exception object at a time|Can declare multiple exceptions|
|`throw new IOException()`|`throws IOException`|

Example:

```java
void test() throws IOException {
    throw new IOException("File error");
}
```

Here:

- `throws` → declaration
    
- `throw` → actual throwing
    

---

# 13. Creating Custom Exceptions

You can create your own exception.

### Unchecked custom exception

```java
class InsufficientBalanceException extends RuntimeException {

    public InsufficientBalanceException(String message) {
        super(message);
    }
}
```

Use it:

```java
class BankAccount {

    void withdraw(double amount) {

        double balance = 1000;

        if (amount > balance) {
            throw new InsufficientBalanceException(
                "Insufficient balance"
            );
        }
    }
}
```

---

# 14. Custom Checked Exception

Extend `Exception`:

```java
class InvalidAgeException extends Exception {

    public InvalidAgeException(String message) {
        super(message);
    }
}
```

Then:

```java
void register(int age) throws InvalidAgeException {

    if (age < 18) {
        throw new InvalidAgeException(
            "Age must be 18 or above"
        );
    }
}
```

Caller:

```java
try {
    register(15);
} catch (InvalidAgeException e) {
    System.out.println(e.getMessage());
}
```

---

# 15. Exception Object

When an exception occurs, Java creates an exception object.

```java
try {
    int x = 10 / 0;
} catch (ArithmeticException e) {
    System.out.println(e);
}
```

You can get useful information:

### `getMessage()`

```java
System.out.println(e.getMessage());
```

Output:

```text
/ by zero
```

### `getClass()`

```java
System.out.println(e.getClass());
```

### `printStackTrace()`

```java
e.printStackTrace();
```

This shows where the exception occurred.

---

# 16. Stack Trace

Example:

```text
java.lang.ArithmeticException: / by zero
    at Main.calculate(Main.java:10)
    at Main.main(Main.java:5)
```

Read it roughly as:

```text
Exception type
    ↓
Message
    ↓
Where exception occurred
    ↓
Who called that method
    ↓
Who called the caller
```

---

# 17. Exception Propagation

Suppose:

```java
void methodA() {
    methodB();
}

void methodB() {
    methodC();
}

void methodC() {
    int x = 10 / 0;
}
```

Exception occurs in:

```text
methodC()
```

If nobody handles it there, it propagates:

```text
methodC()
   ↓
methodB()
   ↓
methodA()
   ↓
main()
```

If `main()` doesn't handle it, the thread terminates.

---

# 18. Try-Catch with Method Calls

```java
public class Main {

    static void divide() {
        int result = 10 / 0;
    }

    public static void main(String[] args) {

        try {
            divide();
        } catch (ArithmeticException e) {
            System.out.println("Handled");
        }

        System.out.println("Continue");
    }
}
```

Output:

```text
Handled
Continue
```

---

# 19. Try-with-Resources

This is very important for Java interviews.

Resources such as:

```text
FileInputStream
BufferedReader
Connection
PreparedStatement
```

often need to be closed.

Instead of:

```java
try {
    // use resource
} finally {
    // close resource
}
```

you can use:

```java
try (FileInputStream input =
         new FileInputStream("test.txt")) {

    // use file

} catch (IOException e) {
    e.printStackTrace();
}
```

Java automatically closes the resource.

The resource must implement:

```java
AutoCloseable
```

or:

```java
Closeable
```

---

# 20. Multiple Resources

```java
try (
    FileInputStream input =
        new FileInputStream("input.txt");

    FileOutputStream output =
        new FileOutputStream("output.txt")
) {

    // use resources

} catch (IOException e) {
    e.printStackTrace();
}
```

Resources are automatically closed.

---

# 21. `finally` and `return`

Interesting interview question:

```java
static int test() {

    try {
        return 10;
    } finally {
        return 20;
    }
}
```

Result:

```text
20
```

The `finally` return overrides the earlier return.

### Avoid this

Don't normally put `return` inside `finally`.

It makes control flow confusing and can suppress exceptions.

---

# 22. Exception Chaining

Sometimes one exception occurs because of another exception.

You can preserve the original cause:

```java
try {

    // database operation

} catch (SQLException e) {

    throw new RuntimeException(
        "Database operation failed", e
    );
}
```

Now:

```java
e.getCause()
```

can give you the original exception.

This is heavily used in real applications.

---

# 23. `getCause()`

```java
Exception original =
    new Exception("Original problem");

Exception wrapper =
    new Exception("Higher-level problem", original);

System.out.println(wrapper.getCause());
```

Conceptually:

```text
Higher-level exception
        ↓
      cause
        ↓
Original exception
```

---

# 24. Suppressed Exceptions

This is particularly relevant to try-with-resources.

```java
try (MyResource resource = new MyResource()) {
    // code
}
```

If both the main operation and resource closing throw exceptions, Java can preserve the closing exception as a **suppressed exception**.

You can access them with:

```java
e.getSuppressed();
```

---

# 25. Exception Handling in Spring Boot

In Spring Boot, you usually don't want every controller to contain:

```java
try {
    ...
} catch (...) {
    ...
}
```

Instead, use global exception handling.

Example:

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

Controller:

```java
@GetMapping("/users/{id}")
public User getUser(@PathVariable Long id) {

    return userService.findById(id)
            .orElseThrow(() ->
                new UserNotFoundException(
                    "User not found"
                )
            );
}
```

Response:

```text
HTTP 404
User not found
```

This gives a clean architecture:

```text
Controller
    ↓
Service
    ↓
Repository
    ↓
Exception
    ↓
@RestControllerAdvice
    ↓
HTTP Response
```

---

# 26. Best Practices

### 1. Don't catch `Exception` unnecessarily

Avoid:

```java
try {
    // code
} catch (Exception e) {
}
```

Prefer a specific exception:

```java
catch (IOException e) {
}
```

---

### 2. Don't swallow exceptions

Bad:

```java
catch (Exception e) {
}
```

You lose the error information.

---

### 3. Give meaningful messages

Bad:

```java
throw new RuntimeException("Error");
```

Better:

```java
throw new UserNotFoundException(
    "User with ID " + id + " was not found"
);
```

---

### 4. Don't use exceptions for normal control flow

Bad design:

```java
try {
    while (true) {
        list.get(index++);
    }
} catch (IndexOutOfBoundsException e) {
    // loop finished
}
```

Use normal conditions instead.

---

### 5. Preserve the original cause

Prefer:

```java
throw new RuntimeException("Operation failed", e);
```

instead of:

```java
throw new RuntimeException("Operation failed");
```

---

# 27. Most Important Interview Questions

You should be able to answer these:

### Basic

1. What is an exception?
    
2. What is exception handling?
    
3. Difference between `Error` and `Exception`.
    
4. Difference between checked and unchecked exceptions.
    
5. What is `RuntimeException`?
    
6. What is `Throwable`?
    

### `try/catch/finally`

7. What is `try`?
    
8. What is `catch`?
    
9. What is `finally`?
    
10. Does `finally` always execute?
    
11. Can we have `try` without `catch`?
    
12. Can we have `try` without `catch` but with `finally`?
    
13. Can we have multiple catch blocks?
    
14. What is multi-catch?
    

### `throw/throws`

15. Difference between `throw` and `throws`.
    
16. Can `throws` declare multiple exceptions?
    
17. Can we throw checked exceptions?
    
18. Can we throw unchecked exceptions?
    

### Advanced

19. What is exception propagation?
    
20. What is exception chaining?
    
21. What is `getCause()`?
    
22. What are suppressed exceptions?
    
23. What is try-with-resources?
    
24. What is `AutoCloseable`?
    
25. What happens if both `try` and `finally` return values?
    
26. What happens if `finally` throws an exception?
    
27. How do you create a custom exception?
    
28. When should you use checked vs unchecked custom exceptions?
    
29. How does exception handling work in Spring Boot?
    
30. What is `@ExceptionHandler`?
    
31. What is `@ControllerAdvice` / `@RestControllerAdvice`?
    

---

## ⭐ Interview Cheat Sheet

```text
Throwable
│
├── Error
│
└── Exception
     │
     ├── RuntimeException
     │    ├── NullPointerException
     │    ├── ArithmeticException
     │    └── IllegalArgumentException
     │
     └── Checked Exceptions
          ├── IOException
          └── SQLException
```

```java
try {
    // risky code
}
catch (SpecificException e) {
    // handle
}
finally {
    // cleanup
}
```

```java
throw new MyException();
```

means **actually throw**.

```java
void test() throws MyException
```

means **declare that the method may throw it**.

For interviews, the most important areas to master are **checked vs unchecked exceptions, `throw` vs `throws`, exception propagation, `finally`, try-with-resources, custom exceptions, exception chaining, and Spring Boot global exception handling**.