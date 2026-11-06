[[java]]

Here’s the difference between **String**, **StringBuilder**, and **StringBuffer** in Java:

| Feature           | `String`                        | `StringBuilder`                      | `StringBuffer`                      |
| ----------------- | ------------------------------- | ------------------------------------ | ----------------------------------- |
| **Mutability**    | Immutable                       | Mutable                              | Mutable                             |
| **Thread Safety** | Yes (immutable)                 | No                                   | Yes (synchronized)                  |
| **Performance**   | Slow for frequent modifications | Fast                                 | Slower than StringBuilder           |
| **Introduced In** | Java 1.0                        | Java 5                               | Java 1.0                            |
| **Use Case**      | Fixed text                      | Single-threaded string modifications | Multi-threaded string modifications |

---

## 1. String

A `String` object cannot be changed after creation.

```java
String s = "Hello";
s.concat(" World");

System.out.println(s);
```

Output:

```java
Hello
```

Because `concat()` creates a new object instead of modifying the existing one.

### Memory Example

```java
String s = "Hello";
s = s + " World";
```

* `"Hello"` remains unchanged
* New object `"Hello World"` is created

### Best For

* Constant text
* Read-only strings
* Security and caching

---

## 2. StringBuilder

`StringBuilder` is mutable and faster because it is **not synchronized**.

```java
StringBuilder sb = new StringBuilder("Hello");
sb.append(" World");

System.out.println(sb);
```

Output:

```java
Hello World
```

### Best For

* Frequent string modifications
* Loops and concatenations
* Single-threaded applications

### Performance

Very fast compared to `String`.

---

## 3. StringBuffer

`StringBuffer` is similar to `StringBuilder`, but methods are synchronized, making it thread-safe.

```java
StringBuffer sb = new StringBuffer("Hello");
sb.append(" World");

System.out.println(sb);
```

Output:

```java
Hello World
```

### Best For

* Multi-threaded environments
* When multiple threads modify the same string object

### Performance

Slower than `StringBuilder` because synchronization adds overhead.

---

# Key Difference Summary

## Mutability

```java
String       -> Immutable
StringBuilder -> Mutable
StringBuffer  -> Mutable
```

## Thread Safety

```java
String        -> Safe (immutable)
StringBuilder -> Not thread-safe
StringBuffer  -> Thread-safe
```

## Speed

```java
Fastest: StringBuilder
Medium : StringBuffer
Slowest for modifications: String
```

---

# Interview-Friendly Answer

* Use **String** when data should not change.
* Use **StringBuilder** for fast string manipulation in single-threaded programs.
* Use **StringBuffer** when thread safety is required.