### Clean Code Principles in Java: Beginner to Advanced

We'll learn these principles using basic Java only — no Spring Boot, frameworks, or complicated code.

The topics are:

* DRY — Don't Repeat Yourself

* KISS — Keep It Simple, Stupid

* YAGNI — You Aren't Gonna Need It

* Law of Demeter — Don't talk to strangers

* Coupling — How dependent classes are on one another

* Cohesion — How closely related a class's responsibilities are

These principles help you write code that is easier to understand, test, maintain, and extend.

### 1\. DRY — Don't Repeat Yourself

Meaning: Avoid duplicating the same logic in multiple places. Put reusable logic in one method or class.

### Bad example: Repeating code

```java
public class Main {
    public static void main(String[] args) {
        int price1 = 100;
        int tax1 = price1 * 18 / 100;
        System.out.println(price1 + tax1);

        int price2 = 200;
        int tax2 = price2 * 18 / 100;
        System.out.println(price2 + tax2);
    }
}
```

The tax calculation is repeated. If the tax rate changes, you must update multiple places.

### Good example: Use a method

```java
public class Main {

    static int calculateTotal(int price) {
        int tax = price * 18 / 100;
        return price + tax;
    }

    public static void main(String[] args) {
        System.out.println(calculateTotal(100));
        System.out.println(calculateTotal(200));
    }
}
```

Now the calculation exists in one place.

When to use DRY: When the same business rule or logic is duplicated and should remain consistent.

Important: DRY doesn't mean eliminating every repeated line. Two pieces of code that look similar but represent different business rules may need to stay separate.

### 2\. KISS — Keep It Simple

Meaning: Prefer the simplest solution that correctly solves the problem.

### Bad example: Unnecessary complexity

```java
public class Main {
    public static void main(String[] args) {
        int age = 22;

        String result = age >= 18
                ? (age > 100 ? "Very old" : "Adult")
                : "Minor";

        System.out.println(result);
    }
}
```

The nested ternary makes a simple decision harder to understand.

### Good example: Simple `if-else`

```java
public class Main {
    public static void main(String[] args) {
        int age = 22;

        if (age >= 18) {
            System.out.println("Adult");
        } else {
            System.out.println("Minor");
        }
    }
}
```

The second version is easier to read and maintain.

When to use KISS: Whenever you're tempted to introduce unnecessary abstractions, complex conditions, or clever code.

KISS does not mean avoiding good design. It means avoiding complexity that provides no real benefit.

### 3\. YAGNI — You Aren't Gonna Need It

Meaning: Don't implement features until they are actually needed.

### Bad example: Building unnecessary features

Suppose you are creating a basic calculator.

```java
class Calculator {
    int add(int a, int b) {
        return a + b;
    }

    int subtract(int a, int b) {
        return a - b;
    }

    int multiply(int a, int b) {
        return a * b;
    }

    int divide(int a, int b) {
        return a / b;
    }

    // Not currently needed:
    // Scientific calculations
    // Graph plotting
    // Currency conversion
    // Cloud synchronization
}
```

The problem isn't having the four useful methods. The problem would be building the extra systems before any requirement calls for them.

### Good example: Implement the current requirement

```java
class Calculator {
    int add(int a, int b) {
        return a + b;
    }
}

public class Main {
    public static void main(String[] args) {
        Calculator calculator = new Calculator();

        System.out.println(calculator.add(10, 20));
    }
}
```

Start with addition if that is all the application currently needs. Add subtraction or other features when required.

When to use YAGNI: When planning future features that aren't part of the current requirements.

Remember: YAGNI doesn't mean ignoring likely needs such as security, correctness, or necessary error handling. It means not building speculative features.

### 4\. Law of Demeter — Don't Talk to Strangers

Meaning: A class should communicate mainly with objects it directly knows, rather than navigating through a long chain of other objects.

Imagine an order system:

* `Customer` has an `Order`.

* `Order` has a `Product`.

* `Product` has a price.

### Bad example: Long chain of calls

```java
class Product {
    double getPrice() {
        return 500;
    }
}

class Order {
    Product product = new Product();

    Product getProduct() {
        return product;
    }
}

class Customer {
    Order order = new Order();

    Order getOrder() {
        return order;
    }
}

public class Main {
    public static void main(String[] args) {
        Customer customer = new Customer();

        double price = customer.getOrder()
                               .getProduct()
                               .getPrice();

        System.out.println(price);
    }
}
```

The `Main` code must know the internal structure of `Customer`, `Order`, and `Product`.

If the order structure changes, code that navigates this chain may also need to change.

### Good example: Ask the order for the price

```java
class Product {
    double getPrice() {
        return 500;
    }
}

class Order {
    private Product product = new Product();

    double getTotal() {
        return product.getPrice();
    }
}

class Customer {
    private Order order = new Order();

    double getOrderTotal() {
        return order.getTotal();
    }
}

public class Main {
    public static void main(String[] args) {
        Customer customer = new Customer();

        System.out.println(customer.getOrderTotal());
    }
}
```

Now the calling code doesn't need to know how an order stores its product.

When to use it: When you see long chains of method calls or classes accessing too many other objects' internal details.

One important detail: the Law of Demeter is a guideline, not a ban on every chained call. For example, `list.stream().filter(...).toList()` is a normal Java Stream pipeline.

### 5\. Coupling — Dependency Between Classes

Meaning: Coupling describes how strongly one class depends on another.

There are two useful concepts:

* Tight coupling: A class depends directly on a specific implementation.

* Loose coupling: A class depends on an abstraction, making implementations easier to replace.

### Bad example: Tight coupling

Suppose you want to send notifications.

```java
class EmailService {
    void send(String message) {
        System.out.println("Email: " + message);
    }
}

class Notification {
    private EmailService email = new EmailService();

    void notifyUser(String message) {
        email.send(message);
    }
}
```

`Notification` directly creates an `EmailService`. Switching to SMS requires modifying the `Notification` class.

### Good example: Loose coupling using an interface

```java
interface MessageService {
    void send(String message);
}

class EmailService implements MessageService {
    public void send(String message) {
        System.out.println("Email: " + message);
    }
}

class SmsService implements MessageService {
    public void send(String message) {
        System.out.println("SMS: " + message);
    }
}

class Notification {
    private MessageService service;

    Notification(MessageService service) {
        this.service = service;
    }

    void notifyUser(String message) {
        service.send(message);
    }
}

public class Main {
    public static void main(String[] args) {
        MessageService service = new EmailService();
        Notification notification = new Notification(service);

        notification.notifyUser("Hello!");

        Notification smsNotification =
                new Notification(new SmsService());

        smsNotification.notifyUser("Hello!");
    }
}
```

Here, `Notification` depends on `MessageService`, not on a particular implementation.

The constructor passes the required object into the class. This is called constructor dependency injection.

### Why loose coupling matters

Easy to replace

Email can be replaced with SMS without rewriting Notification.

Easy to test

You can provide a fake MessageService during testing.

Easy to extend

You can add a new notification implementation without changing the caller.

When to use it: When classes directly create their dependencies, when implementations may change, or when testing is difficult because classes are tightly connected.

Loose coupling does not mean zero dependencies. Classes still depend on each other; the goal is to make those dependencies manageable.

### 6\. Cohesion — How Focused a Class Is

Meaning: Cohesion describes how closely related the responsibilities within a class are.

* High cohesion: A class has one clear, closely related purpose.

* Low cohesion: A class handles many unrelated responsibilities.

### Bad example: Low cohesion

```java
class User {
    void saveUser() {
        System.out.println("Saving user to database");
    }

    void sendEmail() {
        System.out.println("Sending email");
    }

    void generateReport() {
        System.out.println("Generating report");
    }

    void calculateTax() {
        System.out.println("Calculating tax");
    }
}
```

The `User` class handles database operations, email, reporting, and tax calculations. These responsibilities are unrelated.

### Good example: High cohesion

```java
class User {
    void displayName() {
        System.out.println("Alex");
    }
}

class UserRepository {
    void saveUser() {
        System.out.println("Saving user to database");
    }
}

class EmailService {
    void sendEmail() {
        System.out.println("Sending email");
    }
}

class ReportService {
    void generateReport() {
        System.out.println("Generating report");
    }
}
```

Each class now has a clear responsibility.

When to use it: When a class has too many unrelated methods, changes for unrelated reasons, or is difficult to understand.

High cohesion often makes testing and maintenance easier. However, don't create a separate class for every single method; split classes when the responsibilities genuinely differ.

### 7\. Coupling vs. Cohesion

These two concepts are often discussed together.

Coupling = Between classes

How much does one class depend on other classes?

Generally, aim for lower coupling.

Cohesion = Within a class

How closely are the methods and responsibilities in one class related?

Generally, aim for higher cohesion.

Consider an `OrderService` class:

* If it handles order creation, payment, email delivery, and PDF generation itself, it has low cohesion.

* If it directly creates concrete payment and email classes, it may also have high coupling.

* If it focuses on order operations and uses well-defined interfaces for payment and notification, its design is generally better.

### 8\. How all six principles work together

| Principle      | Main question                                             |
| -------------- | --------------------------------------------------------- |
| DRY            | Am I duplicating the same logic?                          |
| KISS           | Can I solve this more simply?                             |
| YAGNI          | Do I actually need this feature now?                      |
| Law of Demeter | Am I reaching too deeply into another object's internals? |
| Low coupling   | Am I too dependent on a specific class?                   |
| High cohesion  | Does this class have a clear responsibility?              |

These principles complement each other, but they are not absolute rules. For example, extracting a method to eliminate duplication can sometimes make code more complicated. Use judgment and prioritize understandable, correct code.

### 9\. Practice exercise

Try improving this code using the principles you just learned.

```java
class Shop {
    void buy() {
        System.out.println("Saving order");
        System.out.println("Sending email");
        System.out.println("Generating PDF");
        System.out.println("Saving order");
    }
}
```

