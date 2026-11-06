[[java]]

# Java `java.lang` Package — Interview Preparation

`java.lang` is one of the **most important Java packages for interviews** because its classes are automatically available in every Java program.

You don't normally need:

```java
import java.lang.String;
```

because Java implicitly imports `java.lang.*`.

## 1. What is `java.lang`?

**Interview answer:**

> `java.lang` is a core Java package that contains fundamental classes and interfaces required for Java programming. It is automatically imported by the Java compiler.

Examples:

```text
java.lang
├── Object
├── String
├── StringBuilder
├── StringBuffer
├── System
├── Math
├── Thread
├── Exception
├── RuntimeException
├── Throwable
├── Integer
├── Double
├── Boolean
└── Class
```

---

# 2. Important Interview Questions

### Q1. Is `java.lang` automatically imported?

**Yes.**

Every Java source file implicitly has:

```java
import java.lang.*;
```

You don't have to write it manually.

---

### Q2. Why is `java.lang` important?

It contains fundamental classes used in almost every Java program.

For example:

```java
String name = "Pavan";
System.out.println(name);
```

Here:

```text
String  → java.lang.String
System  → java.lang.System
```

---

# 3. `Object` — Most Important

Every Java class directly or indirectly inherits from:

```java
java.lang.Object
```

Example:

```java
class Student {
}
```

Conceptually:

```text
Object
   ↑
Student
```

Important `Object` methods:

```java
toString()
equals()
hashCode()
getClass()
clone()
wait()
notify()
notifyAll()
```

### Interview question

**Q: Why is Object called the root class?**

> Because every Java class ultimately inherits from `Object`, either directly or indirectly.

---

# 4. `String`

`String` represents a sequence of characters.

```java
String s = "Hello";
```

Important characteristics:

- `String` is a class.
    
- It is **immutable**.
    
- String literals are stored in the **String Pool**.
    
- It implements `CharSequence`.
    
- It is `final`.
    

Example:

```java
String s = "Hello";

s.concat(" World");

System.out.println(s);
```

Output:

```text
Hello
```

Because `String` is immutable.

Correct:

```java
s = s.concat(" World");
```

---

# 5. `StringBuilder`

Used for mutable strings.

```java
StringBuilder sb = new StringBuilder("Hello");

sb.append(" World");

System.out.println(sb);
```

Output:

```text
Hello World
```

Important interview comparison:

|String|StringBuilder|
|---|---|
|Immutable|Mutable|
|Thread-safe due to immutability|Not synchronized|
|String operations can create objects|Better for repeated modifications|
|Good for fixed text|Good for building strings|

---

# 6. `StringBuffer`

Similar to `StringBuilder`, but its methods are synchronized.

```java
StringBuffer sb = new StringBuffer("Hello");
sb.append(" World");
```

Comparison:

```text
String
   ↓
Immutable

StringBuilder
   ↓
Mutable + not synchronized

StringBuffer
   ↓
Mutable + synchronized
```

Common interview question:

**Q: StringBuilder vs StringBuffer?**

> Both are mutable. `StringBuffer` provides synchronized methods, while `StringBuilder` generally provides better performance when synchronization is unnecessary.

---

# 7. Wrapper Classes

`java.lang` contains wrapper classes for primitive types.

```text
byte    → Byte
short   → Short
int     → Integer
long    → Long
float   → Float
double  → Double
char    → Character
boolean → Boolean
```

Example:

```java
int x = 10;

Integer obj = x;
```

This is **autoboxing**.

```java
Integer obj = 10;

int x = obj;
```

This is **unboxing**.

---

# 8. `Integer`

Very common in interviews.

```java
Integer.parseInt("123");
```

Returns:

```text
123
```

Other useful methods:

```java
Integer.valueOf("123");
Integer.toString(100);
Integer.max(10, 20);
Integer.min(10, 20);
```

### `parseInt()` vs `valueOf()`

```java
Integer.parseInt("10");
```

returns:

```text
int
```

while:

```java
Integer.valueOf("10");
```

returns:

```text
Integer
```

---

# 9. `System`

`System` provides access to system resources and standard I/O.

Most commonly:

```java
System.out.println("Hello");
```

Here:

```text
System → java.lang.System
out    → PrintStream object
println() → method
```

Other examples:

```java
System.currentTimeMillis();

System.nanoTime();

System.exit(0);

System.getProperty("java.version");
```

---

# 10. `Math`

Provides mathematical operations.

```java
Math.max(10, 20);
Math.min(10, 20);
Math.sqrt(25);
Math.pow(2, 3);
Math.abs(-10);
Math.round(10.5);
```

Example:

```java
double x = Math.sqrt(25);

System.out.println(x);
```

Output:

```text
5.0
```

---

# 11. `Thread`

`Thread` is used for multithreading.

Example:

```java
Thread t = new Thread(() -> {
    System.out.println("Running");
});

t.start();
```

Important methods:

```java
start()
run()
sleep()
join()
interrupt()
isAlive()
currentThread()
```

### Important interview question

**Q: Difference between `start()` and `run()`?**

```java
thread.start();
```

creates/schedules a new thread of execution.

```java
thread.run();
```

is simply a normal method call and does **not** create a new thread by itself.

---

# 12. `Throwable`

Exception hierarchy:

```text
Object
   ↓
Throwable
   ├── Error
   │    ├── OutOfMemoryError
   │    └── StackOverflowError
   │
   └── Exception
        ├── RuntimeException
        └── Other Exceptions
```

Important methods:

```java
getMessage()
printStackTrace()
toString()
```

---

# 13. `Exception`

`Exception` represents conditions that applications may handle.

Example:

```java
try {
    int x = 10 / 0;
} catch (Exception e) {
    System.out.println(e.getMessage());
}
```

---

# 14. `RuntimeException`

`RuntimeException` is the superclass of many unchecked exceptions.

Examples:

```text
NullPointerException
ArithmeticException
ArrayIndexOutOfBoundsException
IllegalArgumentException
NumberFormatException
```

Example:

```java
int x = Integer.parseInt("abc");
```

This can throw:

```text
NumberFormatException
```

---

# 15. `Class`

`java.lang.Class` represents class/type information at runtime.

Example:

```java
Class<?> c = String.class;
```

or:

```java
String s = "Hello";

Class<?> c = s.getClass();
```

Used heavily in **reflection**.

---

# 16. `Enum`

Java's enum functionality is represented by `java.lang.Enum`.

Example:

```java
enum Status {
    ACTIVE,
    INACTIVE
}
```

Every enum type implicitly extends `Enum`.

---

# 17. `Comparable`

`java.lang.Comparable` is used for defining an object's **natural ordering**.

```java
class Student implements Comparable<Student> {

    int age;

    public int compareTo(Student other) {
        return this.age - other.age;
    }
}
```

Important method:

```java
compareTo()
```

---

# 18. `Runnable`

`Runnable` represents a task that can be executed by a thread.

```java
Runnable task = () -> {
    System.out.println("Task running");
};

Thread t = new Thread(task);
t.start();
```

Important method:

```java
run()
```

---

# 19. Most Asked `java.lang` Interview Questions

Prepare these especially well:

### Basic

1. What is `java.lang`?
    
2. Why don't we import `java.lang`?
    
3. Which classes are present in `java.lang`?
    
4. Why is `Object` the root class?
    
5. What methods are present in `Object`?
    

### String

6. Why is String immutable?
    
7. String vs StringBuilder?
    
8. StringBuilder vs StringBuffer?
    
9. What is the String Pool?
    
10. `==` vs `equals()` for String?
    
11. What does `intern()` do?
    

### Wrapper classes

12. What are wrapper classes?
    
13. What is autoboxing?
    
14. What is unboxing?
    
15. `Integer.parseInt()` vs `Integer.valueOf()`?
    
16. Why does `Integer` have caching?
    

### Thread

17. `start()` vs `run()`?
    
18. `sleep()` vs `wait()`?
    
19. What is `currentThread()`?
    
20. What does `join()` do?
    

### Exceptions

21. `Exception` vs `RuntimeException`?
    
22. `Error` vs `Exception`?
    
23. Checked vs unchecked exceptions?
    
24. What is `Throwable`?
    
25. Why is `Throwable` above `Exception` and `Error`?
    

### Advanced

26. What is `Class`?
    
27. What is reflection?
    
28. What is `Comparable`?
    
29. What is `Runnable`?
    
30. Why are wrapper classes immutable?
    

## ⭐ Interview shortcut

If an interviewer says **"Explain java.lang package"**, cover these in order:

```text
java.lang
   │
   ├── Object
   │    ├── equals()
   │    ├── hashCode()
   │    └── toString()
   │
   ├── String
   │    ├── Immutable
   │    └── String Pool
   │
   ├── StringBuilder / StringBuffer
   │
   ├── Wrapper Classes
   │    ├── Integer
   │    ├── Double
   │    └── Boolean
   │
   ├── System
   ├── Math
   ├── Thread
   ├── Throwable
   ├── Exception
   ├── RuntimeException
   ├── Class
   ├── Enum
   ├── Comparable
   └── Runnable
```

This covers the **core `java.lang` topics most likely to come up in a Java fresher interview**.