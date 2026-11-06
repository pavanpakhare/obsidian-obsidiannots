[[java]]
# Java Collections Framework — Complete Tutorial

The **Java Collections Framework (JCF)** provides ready-made data structures for storing and manipulating groups of objects.

For interviews, focus on **what to use, why to use it, time complexity, ordering, duplicates, nulls, and thread safety**.

---

## 1. Collection hierarchy

```text
                    Iterable
                       |
                  Collection
             _________|_________
            |         |         |
          List       Set      Queue
           |          |         |
     ArrayList    HashSet    PriorityQueue
     LinkedList   LinkedHashSet
     Vector       TreeSet
                    |
                SortedSet
                    |
                 NavigableSet
                    |
                  TreeSet


                  Map
                   |
        ___________|____________
       |            |           |
    HashMap    LinkedHashMap  TreeMap
       |
   Hashtable
```

⚠️ **Map is NOT a subtype of Collection.**

---

# 2. List

A `List` is used when:

- order matters
    
- duplicates are allowed
    
- you need index-based access
    

```java
List<String> names = new ArrayList<>();

names.add("Pavan");
names.add("Rahul");
names.add("Pavan");

System.out.println(names);
```

Output:

```text
[Pavan, Rahul, Pavan]
```

### Common implementations

|Implementation|Best use|
|---|---|
|`ArrayList`|General-purpose list|
|`LinkedList`|Frequent insertion/removal at ends|
|`Vector`|Legacy synchronized list|
|`CopyOnWriteArrayList`|Many reads, very few writes|

---

# 3. ArrayList

Internally, `ArrayList` uses a **resizable array**.

```java
List<Integer> numbers = new ArrayList<>();

numbers.add(10);
numbers.add(20);
numbers.add(30);

System.out.println(numbers.get(1));
```

Output:

```text
20
```

### Important operations

```java
numbers.add(40);          // add
numbers.add(1, 15);       // insert
numbers.get(2);           // access
numbers.set(2, 25);       // update
numbers.remove(2);        // remove by index
numbers.contains(20);     // search
numbers.size();           // size
numbers.clear();          // remove everything
```

### Complexity

|Operation|ArrayList|
|---|--:|
|`get()`|O(1)|
|`set()`|O(1)|
|add at end|O(1) amortized|
|insert middle|O(n)|
|remove middle|O(n)|
|search|O(n)|

### Use case

```java
List<Product> products = new ArrayList<>();
```

Good when you mainly **read/access elements**.

Example:

```text
E-commerce:
Products displayed on a page
↓
ArrayList
```

---

# 4. LinkedList

`LinkedList` uses a **doubly linked list**.

```java
LinkedList<String> queue = new LinkedList<>();

queue.add("A");
queue.add("B");
queue.addFirst("Start");
queue.addLast("End");

System.out.println(queue);
```

```text
[Start, A, B, End]
```

Useful operations:

```java
addFirst()
addLast()
removeFirst()
removeLast()
getFirst()
getLast()
```

### Complexity

|Operation|LinkedList|
|---|--:|
|add first|O(1)|
|add last|O(1)|
|remove first|O(1)|
|remove last|O(1)|
|get(index)|O(n)|
|search|O(n)|

### Important interview point

Don't automatically choose `LinkedList` whenever you hear "frequent insertion."

For many real applications, **ArrayList is still preferable** because of better memory locality/cache performance.

---

# 5. Set

A `Set` stores **unique elements**.

```java
Set<String> names = new HashSet<>();

names.add("Pavan");
names.add("Rahul");
names.add("Pavan");

System.out.println(names);
```

`Pavan` appears only once.

### Use case

Remove duplicate values:

```java
List<Integer> numbers =
        List.of(1, 2, 2, 3, 3, 4);

Set<Integer> unique =
        new HashSet<>(numbers);
```

Result:

```text
[1, 2, 3, 4]
```

---

# 6. HashSet

`HashSet` uses hashing internally.

```java
Set<String> users = new HashSet<>();

users.add("Pavan");
users.add("Rahul");
users.add("Amit");

System.out.println(users.contains("Pavan"));
```

### Characteristics

- unique elements
    
- no guaranteed order
    
- average O(1) add/search/remove
    
- permits one `null`
    

### Use case

Check whether something already exists:

```java
Set<String> registeredEmails = new HashSet<>();

if (registeredEmails.contains(email)) {
    System.out.println("Already registered");
}
```

This is much better than repeatedly searching an `ArrayList` when you primarily need membership checks.

---

# 7. LinkedHashSet

Maintains **insertion order** while still preventing duplicates.

```java
Set<String> set = new LinkedHashSet<>();

set.add("C");
set.add("A");
set.add("B");

System.out.println(set);
```

```text
[C, A, B]
```

### Use case

Remove duplicates while preserving original order.

```java
List<String> input =
        List.of("A", "B", "A", "C", "B");

List<String> result =
        new ArrayList<>(new LinkedHashSet<>(input));
```

Result:

```text
[A, B, C]
```

---

# 8. TreeSet

`TreeSet` keeps elements **sorted**.

```java
Set<Integer> numbers = new TreeSet<>();

numbers.add(50);
numbers.add(10);
numbers.add(30);

System.out.println(numbers);
```

Output:

```text
[10, 30, 50]
```

Operations are generally:

```text
O(log n)
```

### Use case

When you need:

> Unique + sorted data

Example:

```java
TreeSet<Integer> scores = new TreeSet<>();

scores.add(80);
scores.add(60);
scores.add(90);
```

Useful methods:

```java
scores.first();
scores.last();
scores.lower(80);
scores.higher(80);
scores.floor(80);
scores.ceiling(80);
```

---

# 9. Queue

A `Queue` is generally used for **processing elements in order**.

```java
Queue<String> queue = new LinkedList<>();

queue.offer("A");
queue.offer("B");
queue.offer("C");

System.out.println(queue.poll());
```

Output:

```text
A
```

### Important methods

|Method|Meaning|
|---|---|
|`offer()`|Add|
|`poll()`|Remove + return|
|`peek()`|Look at first|
|`remove()`|Remove|
|`element()`|Look at first|

Prefer:

```java
offer()
poll()
peek()
```

for queue-style programming.

---

# 10. PriorityQueue

Unlike a normal queue, `PriorityQueue` processes elements according to **priority**.

```java
PriorityQueue<Integer> pq =
        new PriorityQueue<>();

pq.offer(30);
pq.offer(10);
pq.offer(20);

System.out.println(pq.poll());
```

Output:

```text
10
```

### Use cases

Very common in:

- Dijkstra's algorithm
    
- scheduling
    
- top K problems
    
- finding smallest/largest elements
    
- task prioritization
    

Example:

```java
PriorityQueue<Integer> minHeap =
        new PriorityQueue<>();
```

For a max heap:

```java
PriorityQueue<Integer> maxHeap =
        new PriorityQueue<>(Comparator.reverseOrder());
```

---

# 11. Deque

`Deque` means **Double Ended Queue**.

You can insert/remove from both ends.

```java
Deque<Integer> deque =
        new ArrayDeque<>();

deque.addFirst(10);
deque.addLast(20);

System.out.println(deque.removeFirst());
```

### `ArrayDeque`

Usually preferred over `LinkedList` when you simply need a stack or deque.

---

# 12. Stack

Java has the old `Stack` class:

```java
Stack<Integer> stack = new Stack<>();

stack.push(10);
stack.push(20);

System.out.println(stack.pop());
```

But for new code, prefer:

```java
Deque<Integer> stack = new ArrayDeque<>();

stack.push(10);
stack.push(20);

System.out.println(stack.pop());
```

### Use cases

Stack is useful for:

- undo operations
    
- expression evaluation
    
- parentheses matching
    
- DFS
    
- browser history
    

---

# 13. Map

A `Map` stores:

```text
key → value
```

Example:

```java
Map<Integer, String> users =
        new HashMap<>();

users.put(1, "Pavan");
users.put(2, "Rahul");

System.out.println(users.get(1));
```

Output:

```text
Pavan
```

Keys must be unique.

```java
users.put(1, "Amit");
```

The previous value for key `1` is replaced.

---

# 14. HashMap

`HashMap` is the most commonly used Map.

```java
Map<String, Integer> marks =
        new HashMap<>();

marks.put("Java", 90);
marks.put("SQL", 80);
marks.put("Spring", 95);
```

### Complexity

Average:

```text
put()     → O(1)
get()     → O(1)
remove()  → O(1)
containsKey() → O(1)
```

### Use case

Fast lookup.

For example:

```java
Map<String, User> usersByEmail =
        new HashMap<>();

usersByEmail.put(user.getEmail(), user);
```

Then:

```java
User user = usersByEmail.get(email);
```

Instead of searching through every user.

---

# 15. LinkedHashMap

Maintains **insertion order**.

```java
Map<Integer, String> map =
        new LinkedHashMap<>();

map.put(3, "C");
map.put(1, "A");
map.put(2, "B");

System.out.println(map);
```

```text
{3=C, 1=A, 2=B}
```

### Useful for

- predictable iteration
    
- maintaining insertion order
    
- simple LRU-cache implementations
    

---

# 16. TreeMap

Stores keys in **sorted order**.

```java
Map<Integer, String> map =
        new TreeMap<>();

map.put(30, "C");
map.put(10, "A");
map.put(20, "B");
```

Result:

```text
{10=A, 20=B, 30=C}
```

Operations:

```text
O(log n)
```

### Use case

When you need:

> Key → value + sorted keys

---

# 17. Hashtable

Legacy class.

```java
Hashtable<Integer, String> table =
        new Hashtable<>();

table.put(1, "A");
```

It is synchronized, but for modern concurrent applications you normally consider:

```java
ConcurrentHashMap
```

instead.

---

# 18. `Collections` utility class

Don't confuse:

```java
Collection
```

with:

```java
Collections
```

`Collections` is a utility class.

Example:

```java
List<Integer> numbers =
        new ArrayList<>(List.of(5, 2, 8, 1));

Collections.sort(numbers);

System.out.println(numbers);
```

Useful methods:

```java
Collections.sort()
Collections.reverse()
Collections.shuffle()
Collections.min()
Collections.max()
Collections.frequency()
Collections.binarySearch()
```

---

# 19. Modern sorting

You can also use:

```java
numbers.sort(Comparator.naturalOrder());
```

Descending:

```java
numbers.sort(Comparator.reverseOrder());
```

Objects:

```java
students.sort(
    Comparator.comparing(Student::getAge)
);
```

Multiple conditions:

```java
students.sort(
    Comparator.comparing(Student::getAge)
              .thenComparing(Student::getName)
);
```

---

# 20. Iterator

Used to traverse collections.

```java
List<String> names =
        new ArrayList<>(List.of("A", "B", "C"));

Iterator<String> iterator =
        names.iterator();

while (iterator.hasNext()) {
    String name = iterator.next();
    System.out.println(name);
}
```

You can safely remove during iteration:

```java
Iterator<String> it = names.iterator();

while (it.hasNext()) {
    if (it.next().equals("B")) {
        it.remove();
    }
}
```

---

# 21. Enhanced for loop

Usually simpler:

```java
for (String name : names) {
    System.out.println(name);
}
```

For Map:

```java
for (Map.Entry<String, Integer> entry
        : marks.entrySet()) {

    System.out.println(
        entry.getKey() + " = " + entry.getValue()
    );
}
```

Modern:

```java
marks.forEach((key, value) ->
    System.out.println(key + " = " + value)
);
```

---

# 22. Important Map methods

### `get()`

```java
map.get("Java");
```

### `getOrDefault()`

```java
int marks =
    map.getOrDefault("Python", 0);
```

### `putIfAbsent()`

```java
map.putIfAbsent("Java", 100);
```

### `containsKey()`

```java
map.containsKey("Java");
```

### `containsValue()`

```java
map.containsValue(100);
```

### `remove()`

```java
map.remove("Java");
```

### `computeIfAbsent()`

Extremely useful.

```java
Map<String, List<String>> groups =
        new HashMap<>();

groups.computeIfAbsent(
    "Java",
    k -> new ArrayList<>()
).add("Spring");
```

This is commonly used for **grouping data**.

---

# 23. Frequency counting

One of the most common interview patterns:

```java
String[] words = {
    "java", "spring", "java", "sql"
};

Map<String, Integer> frequency =
        new HashMap<>();

for (String word : words) {
    frequency.merge(word, 1, Integer::sum);
}
```

Result:

```text
java → 2
spring → 1
sql → 1
```

Very useful for:

- duplicate detection
    
- character frequency
    
- word frequency
    
- counting votes
    
- analytics
    

---

# 24. Grouping

Suppose:

```java
class Employee {
    String name;
    String department;
}
```

You can create:

```text
department → employees
```

using:

```java
Map<String, List<Employee>> employees =
    new HashMap<>();

for (Employee employee : list) {
    employees
        .computeIfAbsent(
            employee.getDepartment(),
            k -> new ArrayList<>()
        )
        .add(employee);
}
```

This pattern is **very important for backend development**.

---

# 25. Immutable collections

Modern Java provides:

```java
List.of()
Set.of()
Map.of()
```

Example:

```java
List<String> names =
        List.of("A", "B", "C");
```

You cannot modify it:

```java
names.add("D"); // UnsupportedOperationException
```

Useful when returning fixed configuration/data.

---

# 26. `Arrays.asList()` vs `List.of()`

### `Arrays.asList()`

```java
List<String> list =
    Arrays.asList("A", "B", "C");
```

You cannot change its size:

```java
list.add("D"); // exception
```

But you can:

```java
list.set(0, "X");
```

### `List.of()`

```java
List<String> list =
    List.of("A", "B", "C");
```

Completely unmodifiable.

---

# 27. Thread-safe collections

Normal collections like:

```java
ArrayList
HashMap
HashSet
```

are **not thread-safe**.

For concurrent applications:

```java
CopyOnWriteArrayList
ConcurrentHashMap
BlockingQueue
ConcurrentLinkedQueue
```

Example:

```java
ConcurrentHashMap<String, Integer> map =
        new ConcurrentHashMap<>();
```

Useful in:

- multithreaded servers
    
- caches
    
- concurrent processing
    
- producer/consumer systems
    

---

# 28. `ConcurrentHashMap`

Very important for backend interviews.

```java
ConcurrentHashMap<String, Integer> map =
        new ConcurrentHashMap<>();

map.put("Java", 10);
```

Multiple threads can safely work with it without synchronizing the entire map.

Common use:

```text
Multiple requests
       ↓
ConcurrentHashMap
       ↓
Shared cache/counter
```

---

# 29. How to choose a collection

This is probably the **most important practical part**.

### Need ordered elements + duplicates?

```java
ArrayList
```

### Need unique elements?

```java
HashSet
```

### Need unique + insertion order?

```java
LinkedHashSet
```

### Need unique + sorted?

```java
TreeSet
```

### Need key-value lookup?

```java
HashMap
```

### Need key-value + insertion order?

```java
LinkedHashMap
```

### Need key-value + sorted keys?

```java
TreeMap
```

### Need FIFO?

```java
ArrayDeque
```

or

```java
LinkedList
```

### Need priority-based processing?

```java
PriorityQueue
```

### Need stack?

```java
ArrayDeque
```

### Need concurrent map?

```java
ConcurrentHashMap
```

---

# 30. Quick comparison

|Collection|Duplicate|Order|Sorted|Typical lookup|
|---|---|---|---|---|
|ArrayList|✅|Insertion|❌|O(n)|
|LinkedList|✅|Insertion|❌|O(n)|
|HashSet|❌|No guarantee|❌|O(1)*|
|LinkedHashSet|❌|Insertion|❌|O(1)*|
|TreeSet|❌|Sorted|✅|O(log n)|
|HashMap|Keys ❌|No guarantee|❌|O(1)*|
|LinkedHashMap|Keys ❌|Insertion|❌|O(1)*|
|TreeMap|Keys ❌|Sorted|✅|O(log n)|
|PriorityQueue|✅|Priority|Heap order|O(log n) insertion|

`*` Average-case complexity.

---

# 31. Most important interview questions

You should know these very well:

### Beginner

1. What is the Collection Framework?
    
2. Difference between Collection and Collections?
    
3. Difference between List, Set and Map?
    
4. ArrayList vs LinkedList?
    
5. HashSet vs TreeSet?
    
6. HashMap vs Hashtable?
    
7. HashMap vs LinkedHashMap?
    
8. HashMap vs TreeMap?
    
9. Why doesn't Map extend Collection?
    
10. Can collections contain null?
    

### Intermediate

11. How does HashMap work internally?
    
12. How does HashSet work?
    
13. What is hashing?
    
14. What is hash collision?
    
15. Why are `equals()` and `hashCode()` important?
    
16. What happens when two keys have the same hash?
    
17. Fail-fast vs fail-safe iterator?
    
18. Iterator vs ListIterator?
    
19. Comparable vs Comparator?
    
20. How does TreeSet maintain sorting?
    

### Advanced

21. How does ConcurrentHashMap work?
    
22. Why isn't HashMap thread-safe?
    
23. ArrayList resizing mechanism?
    
24. How does PriorityQueue work?
    
25. Why should `ArrayDeque` be preferred over Stack?
    
26. What happens if a mutable object is used as a HashMap key?
    
27. What is structural modification?
    
28. How does Java handle HashMap collisions?
    
29. What happens when HashMap exceeds its load factor?
    
30. How would you implement an LRU cache using collections?
    

---

## ⭐ The most important mental model

Instead of memorizing every class, remember this:

```text
                 What do I need?
                       |
        ┌──────────────┼──────────────┐
        ↓              ↓              ↓
      Values       Unique values    Key → Value
        |              |              |
      List            Set             Map
        |              |              |
   ┌────┴────┐    ┌────┼────┐    ┌────┼────┐
   ↓         ↓    ↓    ↓    ↓    ↓    ↓    ↓
ArrayList  ... Hash  Linked Tree Hash Linked Tree
                 Set   Hash   Set Map  Hash  Map
```
