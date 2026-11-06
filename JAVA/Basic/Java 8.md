[[java]]

## Java 8 Features — Summary


### 1. Lambda Expressions

A **lambda expression** is an anonymous function—no method name, return type, or access modifier. Its main purpose is to bring functional-programming benefits into Java.

**Syntax:**

```java
(parameters) -> expression
```

Examples:

```java
() -> System.out.println("Hello");

(a, b) -> System.out.println(a + b);

x -> x * x;
```

Key rules:

- Can have **zero or more parameters**.
    
- Parameter types can often be inferred.
    
- For one parameter, parentheses can usually be omitted.
    
- Multiple statements require `{}`.
    
- Lambdas are used with **functional interfaces**.
    

**Benefits:** less code, improved readability, simpler anonymous classes, and lambdas can be passed as method arguments.

---

### 2. Functional Interface

An interface containing **exactly one abstract method (SAM — Single Abstract Method)** is a functional interface.

Examples mentioned:

- `Runnable` → `run()`
    
- `Comparable` → `compareTo()`
    
- `ActionListener` → `actionPerformed()`
    
- `Callable` → `call()`
    

Java 8 provides:

```java
@FunctionalInterface
interface Calculator {
    int calculate(int a, int b);
}
```

A functional interface can also contain **default and static methods**; the restriction applies to abstract methods.

---

### 3. Lambda vs Anonymous Inner Class

|Anonymous Class|Lambda|
|---|---|
|Anonymous class|Anonymous function|
|Can implement interfaces with multiple abstract methods|Works with a single abstract method|
|Can have instance variables|Cannot declare instance variables|
|`this` refers to anonymous-class object|`this` refers to enclosing object|
|More verbose|More concise|

The PDF emphasizes that a lambda is **not a complete replacement** for every anonymous inner class.

---

### 4. Default Methods

Java 8 allows interfaces to contain concrete methods using `default`.

```java
interface Vehicle {
    default void start() {
        System.out.println("Starting...");
    }
}
```

Implementation classes automatically get the default method and can override it if needed.

**Main purpose:** add new functionality to existing interfaces without breaking existing implementation classes.

If two interfaces have the same default method, the implementing class must resolve the ambiguity:

```java
class Car implements Left, Right {
    @Override
    public void m1() {
        Left.super.m1();
    }
}
```

---

### 5. Static Methods in Interfaces

Java 8 also allows static methods inside interfaces.

```java
interface MathUtil {
    static int add(int a, int b) {
        return a + b;
    }
}
```

Call them using the **interface name**:

```java
MathUtil.add(10, 20);
```

They aren't inherited by implementation classes and aren't overridden.

---

### 6. Predicate

`Predicate<T>` is a functional interface used for **conditional checking**.

```java
Predicate<Integer> p = x -> x > 10;

System.out.println(p.test(100)); // true
System.out.println(p.test(7));   // false
```

Important method:

```java
boolean test(T t)
```

Predicate operations:

```java
p.and(...)
p.or(...)
p.negate()
```

These correspond to logical **AND, OR, NOT**.

**Remember:**

> Predicate → input → `boolean`

---

### 7. Function

`Function<T,R>` is used when you want to **process an input and return a result**.

```java
Function<String, Integer> f = s -> s.length();

System.out.println(f.apply("Durga")); // 5
```

Important method:

```java
R apply(T t)
```

### Predicate vs Function

|Predicate|Function|
|---|---|
|`Predicate<T>`|`Function<T,R>`|
|Conditional checking|Transformation/operation|
|Returns `boolean`|Returns any type|
|`test()`|`apply()`|

---

### 8. Method Reference `::`

A **method reference** provides a shorter alternative to a lambda when an existing method already does the required work.

**Static method:**

```java
ClassName::methodName
```

Example:

```java
Runnable r = Test::m1;
```

**Instance method:**

```java
objectReference::methodName
```

Example:

```java
Interf i = testObject::m2;
```

Main advantage: **code reuse**.

---

### 9. Constructor Reference

The `::` operator can also reference constructors.

```java
ClassName::new
```

Example:

```java
Function<String, Sample> f = Sample::new;
```

This is equivalent conceptually to:

```java
s -> new Sample(s)
```

Argument types must match.

---

# 10. Stream API ⭐

Streams are used to **process objects from collections**.

Create a stream:

```java
Stream<T> stream = collection.stream();
```

Think of it as:

```text
Collection
    ↓
  Stream
    ↓
filter / map
    ↓
processing
    ↓
result
```

### `filter()`

Select elements based on a condition:

```java
List<Integer> result =
    list.stream()
        .filter(x -> x % 2 == 0)
        .collect(Collectors.toList());
```

`filter()` uses a `Predicate`.

### `map()`

Transform each element:

```java
list.stream()
    .map(x -> x + 10)
```

`map()` uses a `Function`.

### Important Stream operations

|Method|Purpose|
|---|---|
|`filter()`|Select elements|
|`map()`|Transform elements|
|`collect()`|Collect results|
|`count()`|Count elements|
|`sorted()`|Sort elements|
|`min()`|Find minimum|
|`max()`|Find maximum|
|`forEach()`|Process each element|
|`toArray()`|Convert to array|
|`Stream.of()`|Create stream from values|

The PDF covers these as the main processing operations.

Example:

```java
List<Integer> even =
    numbers.stream()
           .filter(x -> x % 2 == 0)
           .collect(Collectors.toList());
```

---

# 11. Date and Time API

Java 8 introduced the `java.time` API to provide a more convenient date/time API. The PDF identifies it with the Joda-Time-based API.

### `LocalDate`

For date only:

```java
LocalDate date = LocalDate.now();
```

Get components:

```java
date.getDayOfMonth();
date.getMonthValue();
date.getYear();
```

### `LocalTime`

For time only:

```java
LocalTime time = LocalTime.now();
```

Get:

```java
time.getHour();
time.getMinute();
time.getSecond();
time.getNano();
```

### `LocalDateTime`

For both date and time:

```java
LocalDateTime dt = LocalDateTime.now();
```

You can also create a specific date/time:

```java
LocalDateTime.of(1995, Month.APRIL, 28, 12, 45);
```

And perform operations such as:

```java
dt.plusMonths(6);
dt.minusMonths(6);
```

The PDF also introduces `ZoneId` for representing time zones.

---

# 🧠 Java 8 Cheat Sheet

```text
Java 8
│
├── Lambda
│     └── (x) -> x * x
│
├── Functional Interface
│     └── Exactly 1 abstract method
│
├── Default Methods
│     └── Concrete method inside interface
│
├── Static Interface Methods
│     └── InterfaceName.method()
│
├── Predicate<T>
│     └── T → boolean
│     └── test()
│
├── Function<T,R>
│     └── T → R
│     └── apply()
│
├── Method Reference
│     └── ::
│
├── Constructor Reference
│     └── ClassName::new
│
├── Stream API
│     ├── filter()
│     ├── map()
│     ├── collect()
│     ├── count()
│     ├── sorted()
│     ├── min()
│     ├── max()
│     └── forEach()
│
└── Date/Time API
      ├── LocalDate
      ├── LocalTime
      ├── LocalDateTime
      └── ZoneId
```

### ⭐ Most important for Java/Spring interviews

Focus especially on:

**Lambda → Functional Interface → Predicate/Function → Method Reference → Stream API**

They are closely connected:

```java
List<String> names = List.of("Pavan", "Java", "Spring");

names.stream()
     .filter(s -> s.length() > 4)   // Predicate
     .map(String::toUpperCase)      // Function + Method Reference
     .forEach(System.out::println); // Method Reference
```

This is the core Java 8 style you will frequently encounter in modern Java and Spring Boot code.