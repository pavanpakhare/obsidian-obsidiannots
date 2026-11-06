[[java]]

# Java OOPS — Complete Summary

## 1. Data Hiding

**Data hiding = protecting internal data from direct outside access.**

- Achieved using `private`.
    
- Main advantage: **security**.
    
- Recommended access modifier for data members: `private`.
    

```java
class Account {
    private double balance;
}
```

The outside user accesses data through controlled methods rather than directly.

---

## 2. Abstraction

**Abstraction = hiding implementation details and exposing only required services.**

Java achieves abstraction mainly using:

- `abstract class`
    
- `interface`
    

Example: An ATM exposes operations like withdraw/deposit without exposing its internal implementation.

Main benefits:

- Security
    
- Easier enhancement
    
- Flexibility
    
- Maintainability
    
- Modularity
    
- Easier usage
    

---

## 3. Encapsulation

**Encapsulation = binding data and corresponding methods into one unit.**

The PDF summarizes it as:

> **Encapsulation = Data Hiding + Abstraction**

Typical Java approach:

```java
class Student {
    private int age;

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}
```

Benefits:

- Security
    
- Maintainability
    
- Modularity
    
- Flexibility
    

### Easy difference

|Concept|Main idea|
|---|---|
|Data hiding|Protect data|
|Abstraction|Hide implementation|
|Encapsulation|Combine data + methods|

---

# 4. Tightly Encapsulated Class

A class is **tightly encapsulated** when all its variables are declared `private`.

Getter/setter presence doesn't determine whether the class is tightly encapsulated.

Important rule:

**If a parent class isn't tightly encapsulated, its child class isn't tightly encapsulated either.**

---

# 5. IS-A Relationship — Inheritance

IS-A means **inheritance**.

```java
class Animal {
}

class Dog extends Animal {
}
```

`Dog IS-A Animal`.

- Implemented using `extends`.
    
- Main advantage: **reusability**.
    

### Parent reference + child object

```java
Animal a = new Dog();
```

Valid.

But:

```java
a.dogSpecificMethod();
```

is not directly accessible through the `Animal` reference.

The reference type determines what methods are available at compile time.

### Multiple inheritance

Java doesn't allow:

```java
class C extends A, B { } // invalid
```

because it can create **ambiguity**.

Java supports multiple inheritance through **interfaces**.

### Cyclic inheritance

Not allowed:

```text
A → B → A
```

Java doesn't permit cyclic inheritance.

---

# 6. HAS-A Relationship

HAS-A represents a relationship where one object contains/references another object.

```java
class Engine {
}

class Car {
    Engine engine = new Engine();
}
```

`Car HAS-A Engine`.

Usually implemented using object references, commonly with `new`.

Main advantage: **reusability**.

Potential disadvantage: increased dependency between components.

### Composition vs Aggregation

|Composition|Aggregation|
|---|---|
|Strong relationship|Weak relationship|
|Contained object depends on container|Contained object can exist independently|
|Example: University → Departments|Example: Department → Professors|

The PDF uses university/departments for composition and department/professors for aggregation.

---

# 7. Method Signature

In Java:

**Method signature = method name + argument types**

```java
void add(int a)
void add(double a)
```

These have different signatures.

**Return type is NOT part of method signature.**

Therefore this is invalid:

```java
int methodOne() { }
double methodOne() { }  // duplicate signature
```

---

# 8. Polymorphism

**Polymorphism = same name, different forms.**

The PDF focuses on:

1. Overloading
    
2. Overriding
    

The three major OOP ideas highlighted in the PDF are:

- **Inheritance → Reusability**
    
- **Polymorphism → Flexibility**
    
- **Encapsulation → Security**
    

---

# 9. Method Overloading

Same method name + **different arguments**.

```java
void add() {}
void add(int a) {}
void add(int a, int b) {}
```

This is:

**Compile-time polymorphism / static polymorphism / early binding.**

The compiler decides which method to call based on the available method signatures.

### Automatic promotion

If an exact argument isn't available, Java can promote primitive types.

For example:

```java
void test(int x) {}
void test(float x) {}

test('a');    // int version
test(10L);    // float version
```

But:

```java
test(10.5);   // double
```

cannot automatically go down to `float`.

The PDF also notes:

**Exact match gets highest priority.**

### Varargs

```java
void test(int x) {}
void test(int... x) {}
```

For:

```java
test(10);
```

the normal `int` method gets priority.

Varargs generally gets lower priority and is used when no better match exists.

---

# 10. Method Overriding

Child class provides its own implementation of an inherited parent method.

```java
class Parent {
    void show() {
        System.out.println("Parent");
    }
}

class Child extends Parent {
    @Override
    void show() {
        System.out.println("Child");
    }
}
```

Important:

```java
Parent p = new Child();
p.show();
```

Output:

```text
Child
```

Because overriding is resolved based on the **runtime object**.

Therefore:

**Overriding = Runtime/Dynamic polymorphism = Late binding.**

---

# 11. Important Overriding Rules

### Method name and parameters

Must be the same.

### Return type

Same return type is allowed.

A **covariant return type** is also allowed for object types.

```java
class Parent {
    Object get() {
        return null;
    }
}

class Child extends Parent {
    String get() {
        return null;
    }
}
```

### `private`

Private methods aren't visible to children, so they aren't overridden.

### `final`

A `final` method cannot be overridden.

```java
final void show() {}
```

### Access modifiers

You cannot reduce visibility.

```text
private < default < protected < public
```

For example, a `public` parent method cannot become `protected` in the child.

---

# 12. Checked vs Unchecked Exceptions in Overriding

Checked exceptions have restrictions.

If the child method throws a checked exception, the parent method must throw the same exception or a compatible broader parent exception.

Unchecked exceptions don't have this restriction.

---

# 13. Static Method — Method Hiding

Static methods are **not overridden**.

If both parent and child have static methods with the same signature, it is called:

**Method hiding.**

```java
class Parent {
    static void show() {
        System.out.println("Parent");
    }
}

class Child extends Parent {
    static void show() {
        System.out.println("Child");
    }
}
```

```java
Parent p = new Child();
p.show();
```

Output:

```text
Parent
```

Because static method resolution uses the **reference type**.

### Remember

|Overriding|Method hiding|
|---|---|
|Non-static methods|Static methods|
|Runtime object|Reference type|
|JVM|Compiler|
|Runtime polymorphism|Compile-time polymorphism|

---

# 14. Variables and Inheritance

Variables are **not overridden**.

Variable resolution depends on the **reference type**.

```java
class Parent {
    int x = 888;
}

class Child extends Parent {
    int x = 999;
}

Parent p = new Child();

System.out.println(p.x);
```

Output:

```text
888
```

Even though the actual object is `Child`.

### Very important interview rule

```text
Overridden method → runtime object
Variable → reference type
Static method → reference type
```

---

# 15. Overloading vs Overriding

|Feature|Overloading|Overriding|
|---|---|---|
|Name|Same|Same|
|Arguments|Different|Same|
|Signature|Different|Same|
|Resolution|Compiler|JVM|
|Binding|Early|Late|
|Polymorphism|Compile-time|Runtime|
|Static methods|Can overload|Cannot override|
|Final methods|Can overload|Cannot override|
|Private methods|Can overload|Cannot override|
|Access reduction|N/A|Not allowed|

---

# 16. Static Control Flow

Static members are initialized when the class is loaded.

General sequence:

```text
Identify static members
        ↓
Initialize static variables
        ↓
Execute static blocks
        ↓
main()
```

For a child class:

```text
Parent static initialization
        ↓
Child static initialization
        ↓
Child main()
```

When a child class is loaded, its parent class is loaded first.

### Static blocks

```java
class Test {
    static {
        System.out.println("Hello");
    }
}
```

Static blocks execute during class loading.

Multiple static blocks execute **top to bottom**.

---

# 17. Instance Control Flow

For object creation:

```java
new Test();
```

Instance initialization occurs for **every object**.

Basic sequence:

```text
Identify instance members
        ↓
Instance variable initialization
        ↓
Instance blocks
        ↓
Constructor
```

For a child object:

```text
Parent instance initialization
        ↓
Parent constructor
        ↓
Child instance initialization
        ↓
Child constructor
```

### Static vs Instance

|Static|Instance|
|---|---|
|Class loading|Object creation|
|Usually once|Every object|
|Static variables/blocks|Instance variables/blocks|
|Before main execution|During object creation|

---

# 18. Constructors

A constructor is used primarily for **object initialization**.

```java
class Student {
    String name;

    Student(String name) {
        this.name = name;
    }
}
```

Rules:

- Constructor name = class name.
    
- No return type.
    
- `void` would make it a method, not constructor.
    
- Allowed modifiers: `public`, `protected`, default, `private`.
    
- Constructors can be overloaded.
    
- Constructors are not inherited.
    
- Constructors cannot be overridden.
    

---

# 19. Default Constructor

If you don't write **any constructor**, the compiler provides a default constructor.

```java
class Test {
}
```

Conceptually:

```java
class Test {
    Test() {
        super();
    }
}
```

If you write your own constructor, the compiler **doesn't automatically provide** the no-argument default constructor.

---

# 20. `super()` vs `this()`

### `super()`

Calls parent constructor.

### `this()`

Calls another constructor of the same class.

Both must appear as the **first statement** of a constructor.

You cannot use both simultaneously.

```java
Test() {
    this(10);
}

Test(int x) {
    super();
}
```

### `super` vs `super()`

Don't confuse them:

```text
super() → parent constructor
super.x → parent variable
super.show() → parent method
```

Similarly:

```text
this() → current-class constructor
this.x → current-class variable
this.show() → current-class method
```

---

# 21. Constructor Chaining

Constructors can call each other:

```java
class Test {

    Test() {
        this(10);
    }

    Test(int x) {
        System.out.println(x);
    }
}
```

This is constructor chaining.

But recursive constructor calls are illegal and produce a **compile-time error**.

---

# 22. Abstract Class Constructor

You cannot directly create an object of an abstract class:

```java
new Parent(); // if Parent is abstract
```

But an abstract class **can have a constructor**.

When a child object is created, the parent constructor executes.

Important:

> Creating a child object does **not** create a separate parent object; the parent constructor is executed as part of child initialization.

---

# 23. Recursive Method

### Nested call

One method calls another:

```java
void methodOne() {
    methodTwo();
}
```

### Recursive call

A method calls itself:

```java
void methodOne() {
    methodOne();
}
```

A recursive method without a terminating condition eventually results in a runtime stack-related failure, while recursive constructor invocation is a compile-time error.

---

# 24. Coupling

**Coupling = degree of dependency between components.**

### Tight coupling

Components depend heavily on each other.

Problems:

- Difficult modification
    
- Poor maintainability
    
- Poor reusability
    

Therefore:

**Prefer loose coupling.**

---

# 25. Cohesion

**Cohesion = how well the responsibilities of a component belong together.**

High cohesion means a component has a clear, well-defined responsibility.

Benefits:

- Easier enhancement
    
- Better maintainability
    
- Better reusability
    

### Best design principle

> **Low/loose coupling + High cohesion**

---

# 26. Object Type Casting

A parent reference can hold a child object:

```java
Object o = new String("Hello");
```

But through an `Object` reference you can only access methods known to `Object`.

```java
o.hashCode(); // valid
o.length();   // compile-time error
```

### Upcasting

```java
Parent p = new Child();
```

Usually automatic.

### Downcasting

```java
Child c = (Child) p;
```

Explicit cast required.

If the actual object isn't compatible with the target type:

```text
ClassCastException
```

occurs at runtime.

### Key idea

Casting changes the **reference type**, not the actual object.

---

# 27. `ArrayList` Reference vs `List` Reference

```java
ArrayList list = new ArrayList();
```

vs

```java
List list = new ArrayList();
```

The second is generally more flexible:

```java
List list = new ArrayList();
List list = new LinkedList();
List list = new Vector();
List list = new Stack();
```

Because `List` is the abstraction/interface while the implementation can vary.

Reference type controls which methods are directly accessible.

---

# 28. Ways to Obtain/Create Objects

The PDF discusses several mechanisms:

1. `new`
    
2. Reflection / `newInstance()`
    
3. `clone()`
    
4. Factory methods
    
5. Deserialization
    

Example:

```java
Test t = new Test();
```

Reflection:

```java
Class.forName("Test").newInstance();
```

Clone:

```java
Test t2 = (Test)t1.clone();
```

Factory:

```java
Runtime r = Runtime.getRuntime();
```

Deserialization:

```java
Test t = (Test) ois.readObject();
```

---

# 29. Factory Method

A factory method is a method accessed through the class that provides/returns an object according to its purpose or constraints.

Examples from the PDF:

```java
Runtime.getRuntime();
DateFormat.getInstance();
```

---

# 30. Singleton Class

A singleton class allows only **one object** of the class to be created.

Example idea:

```java
class Test {

    private static Test t;

    private Test() {
    }

    public static Test getTest() {
        if (t == null) {
            t = new Test();
        }
        return t;
    }
}
```

Usage:

```java
Test t1 = Test.getTest();
Test t2 = Test.getTest();

System.out.println(t1 == t2);
```

Output:

```text
true
```

The PDF highlights:

- Private constructor
    
- Static variable
    
- Factory method
    

as the mechanism for creating its singleton example.

### Why Singleton?

Instead of repeatedly creating equivalent objects, one shared object can be reused.

Benefits mentioned:

- Improved memory utilization
    
- Improved performance
    

---

# ⭐ Most Important Interview Rules

Memorize these:

```text
Data Hiding       → private
Abstraction       → abstract class / interface
Encapsulation     → data + methods
Inheritance       → IS-A
Composition       → strong HAS-A
Aggregation       → weak HAS-A

Overloading       → compile time
Overriding        → runtime
Static method     → hiding
Variable          → never overridden

Overloading       → different arguments
Overriding        → same signature

Method resolution:
  Overloading     → reference type / compiler
  Overriding      → runtime object / JVM
  Static method   → reference type
  Variable        → reference type

Constructor:
  Same name as class
  No return type
  Not inherited
  Not overridden
  Can be overloaded

super() → parent constructor
this()  → current-class constructor

Coupling → dependency
Cohesion → responsibility

Best design → Loose coupling + High cohesion

Upcasting   → Parent p = new Child();
Downcasting → Child c = (Child)p;
```

These points cover the core structure and the major interview questions in the PDF.

### One-line mental model

**OOP in Java:**

```text
Protect data       → Encapsulation
Hide implementation→ Abstraction
Reuse code         → Inheritance
Same interface,
different behavior→ Polymorphism
Build relationships→ IS-A / HAS-A
Create/initialize  → Constructors
Good architecture  → Loose coupling + High cohesion
```