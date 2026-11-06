Here are **Java interview questions on access modifiers and `static`**, from beginner to tricky level.[[java]]

## 1. Access Modifiers

### Basic questions

1. What are access modifiers in Java?
    
2. What is the difference between `public`, `private`, `protected`, and default?
    
3. What is the default access modifier in Java?
    
4. Can a class be declared `private`?
    
5. Can a top-level class be declared `protected`?
    
6. What is the scope of a `public` member?
    
7. What is the scope of a `private` member?
    
8. What is the scope of a `protected` member?
    
9. What is package-private/default access?
    
10. Which access modifier provides the highest accessibility?
    

### Important interview question

**Q: Explain `protected` in Java.**

`protected` members are accessible:

- Inside the same class
    
- Inside the same package
    
- In subclasses outside the package
    

```java
class Parent {
    protected int age = 20;
}

class Child extends Parent {
    void show() {
        System.out.println(age);
    }
}
```

A subclass in another package can access the protected member through inheritance, but there are important restrictions on accessing it through arbitrary `Parent` objects.

---

# 2. `public` Questions

### Q1. What does `public` mean?

A `public` member can be accessed from anywhere, provided the class containing it is accessible.

```java
public class Student {
    public String name;
}
```

### Q2. Can a Java class be `public`?

Yes.

```java
public class Student {
}
```

For a public top-level class, the source filename normally must match the class name:

```text
Student.java
```

### Q3. Can a constructor be public?

Yes.

```java
public Student() {
}
```

---

# 3. `private` Questions

### Q1. Can a `private` method be overridden?

**No.**

A private method isn't inherited by subclasses, so it cannot be overridden.

```java
class A {
    private void show() {}
}

class B extends A {
    void show() {}   // This is NOT overriding
}
```

### Q2. Can a constructor be private?

Yes.

Commonly used in:

- Singleton pattern
    
- Utility classes
    
- Factory patterns
    

```java
class Test {
    private Test() {
    }
}
```

### Q3. Can a top-level class be private?

**No.**

But nested classes can be private.

```java
class Outer {
    private class Inner {
    }
}
```

---

# 4. `static` Interview Questions

### Q1. What is `static`?

`static` means the member belongs to the **class rather than individual objects**.

```java
class Student {
    static String college = "ABC";
}
```

All `Student` objects share the same `college` variable.

---

### Q2. Why is `main()` static?

```java
public static void main(String[] args)
```

Because the JVM needs to invoke `main()` **without creating an object of the class**.

---

### Q3. Can a static method access a non-static variable directly?

**No.**

```java
class Test {
    int x = 10;

    static void show() {
        // System.out.println(x); // Error
    }
}
```

You need an object:

```java
static void show() {
    Test obj = new Test();
    System.out.println(obj.x);
}
```

---

### Q4. Can a non-static method access static variables?

**Yes.**

```java
class Test {
    static int x = 10;

    void show() {
        System.out.println(x);
    }
}
```

---

### Q5. Can a static method be overridden?

**No.**

Static methods are **hidden**, not overridden.

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

This is **method hiding**.

---

### Q6. Can a static method be overloaded?

**Yes.**

```java
class Test {

    static void show() {
    }

    static void show(int x) {
    }
}
```

Overloading depends on the method parameters, not whether the method is static.

---

### Q7. Can a constructor be static?

**No.**

Constructors belong to object creation, while `static` members belong to the class.

```java
static Test() { } // ❌ Invalid
```

---

### Q8. Can a static variable be local?

No.

```java
void test() {
    // static int x = 10; // ❌
}
```

A local variable cannot be declared `static`.

---

# 5. Static Block

### Q1. What is a static block?

A static block is executed when the class is initialized.

```java
class Test {

    static {
        System.out.println("Static block");
    }

    public static void main(String[] args) {
        System.out.println("Main");
    }
}
```

Output:

```text
Static block
Main
```

### Q2. How many times does a static block execute?

Normally **once per class initialization** by a particular class loader.

---

# 6. Tricky Interview Questions

### Q1. Can `static` and `abstract` be used together?

**No.**

```java
abstract static void show(); // ❌
```

An abstract method requires overriding through inheritance, while a static method is associated with the class and isn't overridden.

---

### Q2. Can `static` and `final` be used together?

**Yes.**

Very common:

```java
public static final double PI = 3.14159;
```

---

### Q3. Can `static` and `private` be used together?

**Yes.**

```java
private static int count;
```

---

### Q4. Can `static` and `protected` be used together?

**Yes.**

```java
protected static int count;
```

---

### Q5. Can an interface have static methods?

**Yes.**

```java
interface Test {
    static void show() {
        System.out.println("Hello");
    }
}
```

Call it using:

```java
Test.show();
```

---

# 7. Modifier Combination Questions

Know these combinations for interviews:

|Combination|Valid?|
|---|---|
|`public static`|✅|
|`private static`|✅|
|`protected static`|✅|
|`public final`|✅|
|`static final`|✅|
|`static abstract`|❌|
|`static synchronized`|✅|
|`static native`|✅|
|`static transient`|❌ for methods; `transient` applies to fields|
|`static volatile`|❌|
|`private abstract`|❌|
|`private final`|✅|

### Very common interview trap

```java
private abstract void show();
```

❌ Invalid because a `private` method cannot be overridden, while an `abstract` method must be overridden.

---

## 8. Most Important Questions to Prepare

For a fresher Java interview, make sure you can answer these without hesitation:

1. `public` vs `private` vs `protected` vs default?
    
2. What is package-private?
    
3. Explain `protected` outside the package.
    
4. Can a private method be overridden?
    
5. Can a static method be overridden?
    
6. Static method vs instance method?
    
7. Why is `main()` static?
    
8. Can static methods access instance variables?
    
9. Can constructors be static?
    
10. Can static methods be overloaded?
    
11. What is a static block?
    
12. How many times does a static block execute?
    
13. Can `static` and `final` be used together?
    
14. Why can't `static` and `abstract` be combined?
    
15. Can an interface have static methods?
    
16. Can a top-level class be `private` or `protected`?
    
17. Difference between **method hiding and method overriding**.
    
18. What happens when a static variable is modified by one object?
    
19. What is a static nested class?
    
20. Difference between `static`, `final`, and `abstract`.