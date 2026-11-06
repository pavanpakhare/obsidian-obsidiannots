[[java]]

In Java, a **record** is a compact way to create a class whose main purpose is to **hold data**. Records became a standard feature in **Java 16**.

### Basic example

Instead of writing a normal class like this:

```java
public class Person {
    private final String name;
    private final int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String name() {
        return name;
    }

    public int age() {
        return age;
    }

    // equals(), hashCode(), toString() ...
}
```

You can write:

```java
public record Person(String name, int age) {
}
```

Java automatically provides:

* a constructor: `Person(String name, int age)`
* accessors: `name()` and `age()`
* `equals()`
* `hashCode()`
* `toString()`

Usage:

```java
Person p = new Person("Alice", 25);

System.out.println(p.name()); // Alice
System.out.println(p.age());  // 25
System.out.println(p);        // Person[name=Alice, age=25]
```

### Records are immutable-ish

Record components are `final`, so you cannot reassign them:

```java
Person p = new Person("Alice", 25);

// Not allowed
p.age = 30;
```

There are also no setters generated.

However, a record is only **shallowly immutable**. If it contains a mutable object, that object can still change:

```java
record Team(String name, List<String> members) {}

List<String> names = new ArrayList<>();
names.add("Alice");

Team team = new Team("Developers", names);

names.add("Bob"); // The list inside team has effectively changed
```

### Adding methods

Records can contain your own methods:

```java
public record Rectangle(double width, double height) {

    public double area() {
        return width * height;
    }
}
```

```java
Rectangle r = new Rectangle(10, 5);

System.out.println(r.area()); // 50.0
```

You can also validate constructor arguments with a **compact constructor**:

```java
public record Person(String name, int age) {

    public Person {
        if (age < 0) {
            throw new IllegalArgumentException("Age cannot be negative");
        }
    }
}
```

Notice that you don't need:

```java
this.name = name;
this.age = age;
```

Java assigns the components automatically after the compact constructor body.

### Important limitations

A record cannot extend another class because it implicitly extends `java.lang.Record`:

```java
// Not allowed
record Person(String name) extends SomeClass {
}
```

But it **can implement interfaces**:

```java
interface Printable {
    void print();
}

record Person(String name) implements Printable {

    @Override
    public void print() {
        System.out.println(name);
    }
}
```

A useful mental model is:

```text
record = concise data-carrier class
```

Records are especially useful for **DTOs, API responses, configuration values, coordinates, query results, and other value objects** where the object's identity is primarily determined by its data.
