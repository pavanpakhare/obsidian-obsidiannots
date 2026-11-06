[[java]]

# Java Generics — Interview Preparation

Java **Generics** allow you to write classes, interfaces, and methods that work with different data types while providing **compile-time type safety**.

Example:

```java
List<String> names = new ArrayList<>();

names.add("Pavan");
// names.add(10);  // Compile-time error
```

Without generics:

```java
List names = new ArrayList();

names.add("Pavan");
names.add(10);

String name = (String) names.get(0);
```

With generics, explicit casting is usually unnecessary.

---

## 1. Why do we need Generics?

### Main benefits

1. **Type safety**
    
2. **Avoids unnecessary casting**
    
3. **Code reusability**
    
4. **Compile-time error detection**
    

```java
List<Integer> numbers = new ArrayList<>();

numbers.add(10);
numbers.add(20);

Integer n = numbers.get(0);
```

Interview answer:

> Generics provide compile-time type safety and allow classes and methods to operate on different types without explicitly casting objects.

---

# 2. Generic Class

A class can have a type parameter.

```java
class Box<T> {
    private T value;

    public void set(T value) {
        this.value = value;
    }

    public T get() {
        return value;
    }
}
```

Usage:

```java
Box<String> box1 = new Box<>();
box1.set("Hello");

Box<Integer> box2 = new Box<>();
box2.set(100);
```

Here `T` is a **type parameter**.

---

# 3. Generic Method

A method can have its own generic type.

```java
public static <T> void print(T value) {
    System.out.println(value);
}
```

Usage:

```java
print("Hello");
print(100);
print(10.5);
```

Important syntax:

```java
<T> void method(T value)
```

`<T>` before the return type declares the type parameter.

---

# 4. Multiple Type Parameters

You can have multiple type parameters.

```java
class Pair<K, V> {
    K key;
    V value;

    Pair(K key, V value) {
        this.key = key;
        this.value = value;
    }
}
```

Usage:

```java
Pair<Integer, String> pair =
        new Pair<>(1, "Pavan");
```

Common naming conventions:

|Symbol|Meaning|
|---|---|
|`T`|Type|
|`E`|Element|
|`K`|Key|
|`V`|Value|
|`N`|Number|
|`R`|Return type|

These are conventions, not language requirements.

---

# 5. Bounded Generics

You can restrict what types can be used.

```java
class Calculator<T extends Number> {
    T value;
}
```

Now:

```java
Calculator<Integer> c1 = new Calculator<>();
Calculator<Double> c2 = new Calculator<>();
```

But:

```java
// Calculator<String> c3 = new Calculator<>();
```

❌ Not allowed because `String` doesn't extend `Number`.

---

# 6. Multiple Bounds

A type parameter can have multiple bounds.

```java
<T extends Number & Comparable<T>>
```

Example:

```java
public <T extends Number & Comparable<T>>
void process(T value) {
    // ...
}
```

Important:

**Class must come first, interfaces afterward.**

```java
<T extends Number & Comparable<T>>
```

Not:

```java
<T extends Comparable<T> & Number>
```

---

# 7. Wildcards `<?>`

Wildcard means **unknown type**.

```java
List<?> list;
```

It can refer to:

```java
List<String>
List<Integer>
List<Double>
```

Example:

```java
public void printList(List<?> list) {
    for (Object obj : list) {
        System.out.println(obj);
    }
}
```

---

# 8. `? extends`

Used when you want to **read** from a generic structure.

```java
List<? extends Number> numbers;
```

It can refer to:

```java
List<Integer>
List<Double>
List<Float>
```

Example:

```java
public double sum(List<? extends Number> list) {
    double result = 0;

    for (Number n : list) {
        result += n.doubleValue();
    }

    return result;
}
```

You can read values as `Number`.

But you generally **cannot add** an `Integer`, `Double`, etc.:

```java
// list.add(10); // ❌
```

---

# 9. `? super`

Used when you want to **write/add** values.

```java
List<? super Integer> list;
```

Possible types:

```java
List<Integer>
List<Number>
List<Object>
```

Therefore:

```java
list.add(10);
```

is allowed.

But when retrieving:

```java
Object value = list.get(0);
```

because the exact generic type is unknown.

---

# 10. PECS — Very Important Interview Question

**PECS = Producer Extends, Consumer Super**

### Producer → `extends`

If you're mainly **reading/producing** values:

```java
List<? extends Number>
```

### Consumer → `super`

If you're mainly **adding/consuming** values:

```java
List<? super Integer>
```

Easy way to remember:

> **Get → Extends**  
> **Put → Super**

---

# 11. Generic Type vs Wildcard

### Generic type

```java
public <T> void print(T value)
```

You introduce a named type `T`.

### Wildcard

```java
public void print(List<?> list)
```

You don't care what the exact type is.

For example:

```java
<T> T getFirst(List<T> list)
```

Here you can preserve the relationship between input and output.

---

# 12. Generic Interface

```java
interface Repository<T> {
    void save(T object);

    T findById(int id);
}
```

Implementation:

```java
class UserRepository implements Repository<User> {

    public void save(User user) {
        // save user
    }

    public User findById(int id) {
        return new User();
    }
}
```

---

# 13. Generic Constructor

Constructors can also use generic parameters.

```java
class Test {

    <T> Test(T value) {
        System.out.println(value);
    }
}
```

Usage:

```java
new Test("Hello");
new Test(100);
```

---

# 14. Can primitive types be used with Generics?

**No.**

This is invalid:

```java
List<int> list; // ❌
```

Use wrapper classes:

```java
List<Integer> list; // ✅
```

Similarly:

```text
int     → Integer
long    → Long
double  → Double
float   → Float
boolean → Boolean
char    → Character
```

This happens because Java generics work with **reference types**, not primitive types.

---

# 15. Type Erasure — Very Important

Java implements generics using **type erasure**.

For example:

```java
List<String>
```

At runtime, generic type information is largely erased, and it behaves essentially as:

```java
List
```

The compiler uses generic information to provide type checking.

Example:

```java
List<String> names = new ArrayList<>();
names.add("Pavan");

String name = names.get(0);
```

The compiler effectively handles the necessary cast.

### Interview answer

> Java generics are implemented mainly through type erasure. Generic type information is used by the compiler for type checking and is generally erased from the runtime representation.

---

# 16. Why can't we do this?

```java
T obj = new T(); // ❌
```

Because Java doesn't know the actual runtime type of `T`.

Instead, you can pass a factory/supplier:

```java
public static <T> T create(Supplier<T> supplier) {
    return supplier.get();
}
```

Usage:

```java
User user = create(User::new);
```

---

# 17. Why can't we create generic arrays?

This is problematic:

```java
T[] array = new T[10]; // ❌
```

Because Java's arrays are reified at runtime, while generic type information is erased.

You can commonly use:

```java
List<T> list = new ArrayList<>();
```

instead.

---

# 18. Generic Collections

Generics are heavily used in the Collections Framework:

```java
List<String>
Set<Integer>
Map<String, Integer>
Queue<Double>
```

Example:

```java
Map<String, Integer> marks = new HashMap<>();

marks.put("Java", 90);
marks.put("Spring", 85);
```

Without generics, you would need casting when retrieving values.

---

# 19. Important Interview Question: Is `List<String>` a subtype of `List<Object>`?

**No.**

This is one of the most important generic concepts.

```java
List<String> strings = new ArrayList<>();

// List<Object> objects = strings; // ❌
```

Why?

If this were allowed:

```java
objects.add(100);
```

Then `strings` would contain an `Integer`, violating its type guarantee.

Therefore:

```text
List<String> ≠ List<Object>
```

This is called **invariance**.

---

# 20. How do you accept any List?

Use:

```java
List<?> list
```

Example:

```java
void print(List<?> list) {
    for (Object obj : list) {
        System.out.println(obj);
    }
}
```

Now:

```java
print(List.of("A", "B"));
print(List.of(1, 2, 3));
```

works.

---

# 21. `T extends Number` vs `? extends Number`

### Type parameter

```java
<T extends Number>
```

You give the type a name and can use it in multiple places.

```java
<T extends Number>
T process(T value)
```

### Wildcard

```java
<? extends Number>
```

You only care that the type is some subtype of `Number`.

```java
void process(List<? extends Number> list)
```

### Interview shortcut

> Use `T` when you need to refer to the same type. Use `?` when the exact type doesn't matter.

---

# 22. Can static members use class type parameters?

No.

```java
class Test<T> {

    // static T value; // ❌
}
```

Why?

`T` belongs to the **instance/class parameterization**, while static members belong to the class itself.

But a static method can introduce its own type:

```java
class Test<T> {

    static <E> void print(E value) {
        System.out.println(value);
    }
}
```

---

# 23. Can a generic class extend another generic class?

Yes.

```java
class Parent<T> {
    T value;
}

class Child<T> extends Parent<T> {
}
```

You can also specify a concrete type:

```java
class StringChild extends Parent<String> {
}
```

---

# 24. Raw Types

A raw type means using a generic class without specifying the type.

```java
List list = new ArrayList();
```

This is allowed for backward compatibility but should generally be avoided.

Prefer:

```java
List<String> list = new ArrayList<>();
```

Raw types lose compile-time type safety.

---

# 25. Diamond Operator `<>`

Instead of:

```java
List<String> names =
    new ArrayList<String>();
```

Java allows:

```java
List<String> names =
    new ArrayList<>();
```

The compiler infers the generic type.

---

# Top Generics Interview Questions

### Beginner

1. What are Generics in Java?
    
2. Why were Generics introduced?
    
3. What are the advantages of Generics?
    
4. What is a generic class?
    
5. What is a generic method?
    
6. What is a generic interface?
    
7. What does `<T>` mean?
    
8. What are `T`, `E`, `K`, and `V`?
    
9. Can Generics work with primitive types?
    
10. What is the diamond operator?
    

### Intermediate

11. What is a bounded type parameter?
    
12. What is `extends` in Generics?
    
13. What is `super` in Generics?
    
14. What is a wildcard?
    
15. Difference between `T` and `?`.
    
16. Difference between `? extends` and `? super`.
    
17. Explain PECS.
    
18. Why is `List<String>` not `List<Object>`?
    
19. What are raw types?
    
20. What is type erasure?
    

### Advanced

21. Why can't you instantiate `T`?
    
22. Why can't you create `new T[]`?
    
23. Can static members use generic type parameters?
    
24. Can a generic class have multiple type parameters?
    
25. Can a generic method exist inside a non-generic class?
    
26. What are multiple bounds?
    
27. Can a generic class extend another generic class?
    
28. What happens to Generics at runtime?
    
29. Why don't Java Generics support primitive types?
    
30. Explain type erasure with an example.
    

---

## ⭐ 5 questions you should definitely prepare

For a Java fresher interview, be able to explain these without memorizing:

```text
1. What are Generics and why do we need them?
             ↓
2. T vs ?
             ↓
3. ? extends vs ? super
             ↓
4. What is PECS?
             ↓
5. What is type erasure?
```

A particularly good interview example is:

```java
public void copy(
    List<? extends Number> source,
    List<? super Number> destination
) {
    for (Number n : source) {
        destination.add(n);
    }
}
```

This single example lets you explain **wildcards, bounds, `extends`, `super`, PECS, type safety, and collections** together.