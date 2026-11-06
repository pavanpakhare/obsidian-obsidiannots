[[java]]

**Normally, no.** A Java application's traditional JVM entry point must be `static`.

```java
public class Test {
    public static void main(String[] args) {
        System.out.println("Hello");
    }
}
```

### Why?

`static` means the method belongs to the **class**, so the JVM can invoke it without creating an object:

```java
Test.main(args);
```

Without `static`:

```java
public class Test {
    public void main(String[] args) {
        System.out.println("Hello");
    }
}
```

The method requires an object:

```java
Test obj = new Test();
obj.main(args);
```

The traditional Java launcher doesn't start your program this way.

### But there is an important modern Java exception

**Java 25 introduced flexible/instance `main` methods for simple source-code launch**, so newer Java can support forms such as:

```java
class Test {
    void main() {
        System.out.println("Hello");
    }
}
```



