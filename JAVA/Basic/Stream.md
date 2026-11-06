[[java]]

# Java Stream API — Interview Tutorial

Java Stream API is one of the **most frequently asked Java 8+ interview topics**. Focus on understanding the flow:

```text
Collection
   ↓
stream()
   ↓
Intermediate operations
   ↓
Terminal operation
   ↓
Result
```

Example:

```java
List<Integer> numbers = List.of(10, 15, 20, 25, 30);

List<Integer> result = numbers.stream()
        .filter(n -> n > 20)
        .map(n -> n * 2)
        .toList();

System.out.println(result); // [50, 60]
```

---

## 1. What is Stream API?

A **Stream** is a sequence of elements that supports functional-style operations for processing data.

```java
List<String> names = List.of("Pavan", "Rahul", "Amit");

names.stream()
     .filter(name -> name.length() > 4)
     .forEach(System.out::println);
```

### Interview answer

> Stream API provides a declarative way to process collections of data using operations such as filtering, mapping, sorting, and reducing.

### Important

A Stream:

- does **not store data**
    
- does **not modify the original collection** by default
    
- supports functional-style operations
    
- can be processed sequentially or in parallel
    
- is generally **single-use**
    

---

# 2. Stream Pipeline

A stream pipeline has three parts:

```text
Source
  ↓
Intermediate operations
  ↓
Terminal operation
```

Example:

```java
List<Integer> numbers = List.of(1, 2, 3, 4, 5);

numbers.stream()                 // Source
       .filter(n -> n % 2 == 0)  // Intermediate
       .map(n -> n * 10)         // Intermediate
       .forEach(System.out::println); // Terminal
```

Output:

```text
20
40
```

---

# 3. Creating Streams

### From Collection

```java
List<Integer> list = List.of(1, 2, 3);

Stream<Integer> stream = list.stream();
```

### From Array

```java
int[] arr = {1, 2, 3};

IntStream stream = Arrays.stream(arr);
```

### Using Stream.of()

```java
Stream<String> stream =
        Stream.of("Java", "Spring", "SQL");
```

### Empty Stream

```java
Stream<String> stream = Stream.empty();
```

---

# 4. Intermediate vs Terminal Operations

This is **very important for interviews**.

### Intermediate operations

Return another Stream.

Examples:

```text
filter()
map()
flatMap()
distinct()
sorted()
limit()
skip()
peek()
```

Example:

```java
stream.filter(...)
      .map(...)
      .sorted();
```

They are generally **lazy**.

---

### Terminal operations

Produce a final result and terminate the stream.

Examples:

```text
forEach()
collect()
toList()
reduce()
count()
min()
max()
findFirst()
findAny()
anyMatch()
allMatch()
noneMatch()
```

Example:

```java
long count = numbers.stream()
        .filter(n -> n > 10)
        .count();
```

---

# 5. filter()

Used to select elements.

```java
List<Integer> numbers =
        List.of(10, 15, 20, 25, 30);

List<Integer> result = numbers.stream()
        .filter(n -> n % 2 == 0)
        .toList();

System.out.println(result);
```

Output:

```text
[10, 20, 30]
```

### Interview question

**Q: Does `filter()` modify the original collection?**

No.

---

# 6. map()

Used to **transform each element**.

```java
List<String> names =
        List.of("java", "spring", "docker");

List<String> result = names.stream()
        .map(String::toUpperCase)
        .toList();
```

Output:

```text
[JAVA, SPRING, DOCKER]
```

Another example:

```java
List<Integer> numbers = List.of(1, 2, 3, 4);

List<Integer> result = numbers.stream()
        .map(n -> n * n)
        .toList();
```

```text
[1, 4, 9, 16]
```

### Remember

```text
filter → selects
map    → transforms
```

---

# 7. flatMap()

One of the most important interview topics.

Suppose:

```java
List<List<Integer>> numbers = List.of(
        List.of(1, 2),
        List.of(3, 4),
        List.of(5, 6)
);
```

Using `map()`:

```java
numbers.stream()
       .map(list -> list.stream())
```

You get:

```text
Stream<Stream<Integer>>
```

Using `flatMap()`:

```java
List<Integer> result = numbers.stream()
        .flatMap(List::stream)
        .toList();
```

Result:

```text
[1, 2, 3, 4, 5, 6]
```

### Interview definition

> `flatMap()` transforms each element into a stream and then flattens all resulting streams into a single stream.

---

# 8. distinct()

Removes duplicates.

```java
List<Integer> numbers =
        List.of(1, 2, 2, 3, 3, 4);

List<Integer> result = numbers.stream()
        .distinct()
        .toList();
```

Output:

```text
[1, 2, 3, 4]
```

It uses `equals()` and `hashCode()` to determine duplicates.

---

# 9. sorted()

### Natural ordering

```java
List<Integer> result = numbers.stream()
        .sorted()
        .toList();
```

### Reverse order

```java
List<Integer> result = numbers.stream()
        .sorted(Comparator.reverseOrder())
        .toList();
```

### Custom sorting

```java
employees.stream()
        .sorted(Comparator.comparing(Employee::getSalary))
        .toList();
```

---

# 10. limit()

Limits the number of elements.

```java
List<Integer> result = numbers.stream()
        .limit(3)
        .toList();
```

If:

```text
[10, 20, 30, 40, 50]
```

Result:

```text
[10, 20, 30]
```

---

# 11. skip()

Skips elements.

```java
numbers.stream()
        .skip(2)
        .toList();
```

For:

```text
[10, 20, 30, 40, 50]
```

Result:

```text
[30, 40, 50]
```

---

# 12. reduce()

Used to combine elements into a single result.

Example: sum.

```java
List<Integer> numbers =
        List.of(10, 20, 30, 40);

int sum = numbers.stream()
        .reduce(0, (a, b) -> a + b);
```

Result:

```text
100
```

Can also use:

```java
int sum = numbers.stream()
        .reduce(0, Integer::sum);
```

### Interview definition

> `reduce()` combines stream elements into a single value using an accumulation operation.

---

# 13. count()

```java
long count = numbers.stream()
        .filter(n -> n > 20)
        .count();
```

---

# 14. min() and max()

```java
Optional<Integer> min =
        numbers.stream().min(Integer::compareTo);

Optional<Integer> max =
        numbers.stream().max(Integer::compareTo);
```

Why `Optional`?

Because the stream could be empty.

---

# 15. findFirst()

```java
Optional<Integer> result =
        numbers.stream()
                .filter(n -> n > 20)
                .findFirst();
```

---

# 16. findAny()

```java
Optional<Integer> result =
        numbers.stream()
                .filter(n -> n > 20)
                .findAny();
```

Important difference:

```text
findFirst() → first element according to encounter order
findAny()   → any matching element
```

`findAny()` can be particularly useful with parallel streams.

---

# 17. anyMatch(), allMatch(), noneMatch()

### anyMatch()

```java
boolean result =
        numbers.stream()
               .anyMatch(n -> n > 100);
```

Means:

> Is there at least one matching element?

---

### allMatch()

```java
boolean result =
        numbers.stream()
               .allMatch(n -> n > 0);
```

Means:

> Do all elements satisfy the condition?

---

### noneMatch()

```java
boolean result =
        numbers.stream()
               .noneMatch(n -> n < 0);
```

Means:

> Does no element satisfy the condition?

---

# 18. Collectors

Very important for interviews.

```java
List<String> result = names.stream()
        .filter(name -> name.length() > 4)
        .collect(Collectors.toList());
```

Modern Java can also use:

```java
List<String> result = names.stream()
        .filter(name -> name.length() > 4)
        .toList();
```

---

## Collect to Set

```java
Set<String> result = names.stream()
        .collect(Collectors.toSet());
```

---

# 19. groupingBy()

Extremely common interview question.

Suppose:

```java
class Employee {
    String name;
    String department;
    double salary;
}
```

Group employees by department:

```java
Map<String, List<Employee>> result =
        employees.stream()
                .collect(
                    Collectors.groupingBy(
                        Employee::getDepartment
                    )
                );
```

Conceptually:

```text
IT       → [Employee1, Employee2]
HR       → [Employee3]
Finance  → [Employee4, Employee5]
```

---

# 20. Grouping + Counting

```java
Map<String, Long> result =
        employees.stream()
                .collect(
                    Collectors.groupingBy(
                        Employee::getDepartment,
                        Collectors.counting()
                    )
                );
```

Result:

```text
IT       → 5
HR       → 3
Finance  → 4
```

---

# 21. partitioningBy()

Splits elements into two groups based on a condition.

```java
Map<Boolean, List<Integer>> result =
        numbers.stream()
                .collect(
                    Collectors.partitioningBy(
                        n -> n % 2 == 0
                    )
                );
```

Conceptually:

```text
true  → even numbers
false → odd numbers
```

### Difference

```text
groupingBy     → multiple groups
partitioningBy → exactly two groups
```

---

# 22. Joining Strings

```java
List<String> names =
        List.of("Java", "Spring", "Docker");

String result = names.stream()
        .collect(Collectors.joining(", "));
```

Result:

```text
Java, Spring, Docker
```

---

# 23. Lazy Evaluation

This is a **very common interview question**.

```java
numbers.stream()
       .filter(n -> {
           System.out.println("filter: " + n);
           return n > 2;
       });
```

Nothing happens because there is no terminal operation.

Add:

```java
.count();
```

Now the stream executes.

### Why?

Intermediate operations are **lazy**.

---

# 24. Short-Circuit Operations

Some operations can stop processing early.

Examples:

```text
findFirst()
findAny()
anyMatch()
allMatch()
noneMatch()
limit()
```

Example:

```java
boolean result = numbers.stream()
        .anyMatch(n -> n > 100);
```

Once a matching element is found, processing can stop.

---

# 25. Stream Cannot Be Reused

This is a common interview question.

```java
Stream<Integer> stream =
        numbers.stream();

stream.count();

stream.forEach(System.out::println);
```

This throws:

```text
IllegalStateException
```

A stream should generally be used once.

Create another stream if needed:

```java
numbers.stream().count();

numbers.stream().forEach(System.out::println);
```

---

# 26. Stream vs Collection

|Collection|Stream|
|---|---|
|Stores data|Processes data|
|Can be reused|Generally single-use|
|Data structure|Processing pipeline|
|Eager|Lazy operations|
|Can add/remove elements depending on type|Doesn't store elements|
|External iteration commonly used|Internal iteration|

Simple interview answer:

> Collection is primarily used to store and manage data, while Stream is used to process data.

---

# 27. map() vs flatMap()

Very common.

```text
map()
   ↓
one element → one result

flatMap()
   ↓
one element → multiple results
   ↓
flattened into one stream
```

Example:

```java
List<List<String>> data = List.of(
        List.of("A", "B"),
        List.of("C", "D")
);
```

```java
data.stream()
    .flatMap(List::stream)
    .forEach(System.out::println);
```

Output:

```text
A
B
C
D
```

---

# 28. Primitive Streams

Java provides:

```text
IntStream
LongStream
DoubleStream
```

Example:

```java
int sum = IntStream.range(1, 6)
        .sum();
```

Result:

```text
15
```

Why?

Primitive streams can avoid unnecessary boxing/unboxing when working with primitive values.

---

# 29. Parallel Stream

```java
numbers.parallelStream()
       .forEach(System.out::println);
```

Or:

```java
numbers.stream()
       .parallel()
       .forEach(System.out::println);
```

### Important interview point

Parallel streams do **not automatically mean faster**.

They can introduce:

- thread-management overhead
    
- synchronization issues
    
- ordering differences
    
- problems with shared mutable state
    

Use them when the workload and data size make parallel processing appropriate.

---

# 30. forEach() vs forEachOrdered()

With parallel streams:

```java
numbers.parallelStream()
       .forEach(System.out::println);
```

Order is not guaranteed.

```java
numbers.parallelStream()
       .forEachOrdered(System.out::println);
```

Encounter order is preserved where the stream has an encounter order.

---

# 31. peek()

Used mainly for debugging/observing elements.

```java
numbers.stream()
       .filter(n -> n > 10)
       .peek(n -> System.out.println("After filter: " + n))
       .map(n -> n * 2)
       .toList();
```

Don't normally use `peek()` for important business logic.

---

# 32. Important Interview Coding Questions

### Q1. Find even numbers

```java
List<Integer> even = numbers.stream()
        .filter(n -> n % 2 == 0)
        .toList();
```

---

### Q2. Find duplicate elements

One approach:

```java
Set<Integer> seen = new HashSet<>();

Set<Integer> duplicates = numbers.stream()
        .filter(n -> !seen.add(n))
        .collect(Collectors.toSet());
```

---

### Q3. Find maximum salary

```java
Optional<Employee> employee =
        employees.stream()
                .max(Comparator.comparing(Employee::getSalary));
```

---

### Q4. Sort employees by salary

```java
List<Employee> result =
        employees.stream()
                .sorted(
                    Comparator.comparing(Employee::getSalary)
                )
                .toList();
```

Descending:

```java
.sorted(
    Comparator.comparing(Employee::getSalary).reversed()
)
```

---

### Q5. Get employee names

```java
List<String> names =
        employees.stream()
                .map(Employee::getName)
                .toList();
```

---

### Q6. Find employees with salary > 50,000

```java
List<Employee> result =
        employees.stream()
                .filter(e -> e.getSalary() > 50000)
                .toList();
```

---

### Q7. Find second-highest salary

```java
Optional<Double> secondHighest =
        employees.stream()
                .map(Employee::getSalary)
                .distinct()
                .sorted(Comparator.reverseOrder())
                .skip(1)
                .findFirst();
```

Flow:

```text
employees
   ↓
map salary
   ↓
distinct
   ↓
descending sort
   ↓
skip highest
   ↓
findFirst
```

---

# 33. Most Important Interview Questions

Prepare these especially well:

1. **What is Stream API?**
    
2. **Stream vs Collection?**
    
3. **Intermediate vs terminal operations?**
    
4. **Why are intermediate operations lazy?**
    
5. **What is `filter()`?**
    
6. **What is `map()`?**
    
7. **`map()` vs `flatMap()`?**
    
8. **What is `reduce()`?**
    
9. **What is `collect()`?**
    
10. **`findFirst()` vs `findAny()`?**
    
11. **`anyMatch()` vs `allMatch()` vs `noneMatch()`?**
    
12. **What is `groupingBy()`?**
    
13. **`groupingBy()` vs `partitioningBy()`?**
    
14. **What is a parallel stream?**
    
15. **Sequential vs parallel stream?**
    
16. **Can a Stream be reused?**
    
17. **Does Stream modify the original collection?**
    
18. **What is short-circuiting?**
    
19. **What is `peek()`?**
    
20. **Why use `IntStream`, `LongStream`, `DoubleStream`?**
    

### One-line memory trick

```text
filter  → select
map     → transform
flatMap → flatten
distinct → remove duplicates
sorted  → order
limit   → take first N
skip    → ignore first N
reduce  → combine
collect → gather
groupingBy → group
partitioningBy → split into 2
findFirst → first
findAny → any
count → count
```
