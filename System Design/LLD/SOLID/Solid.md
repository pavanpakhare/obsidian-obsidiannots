### SOLID Principles in Java — Beginner Tutorial

SOLID is a set of five object-oriented design principles that help you write Java code that is easy to understand, maintain, test, and extend.

We'll learn each principle using basic Java classes, without Spring Boot or complicated frameworks.

### The five SOLID principles

### S

Single Responsibility Principle (SRP)

A class should have one responsibility.

### O

Open/Closed Principle (OCP)

Extend behavior without constantly modifying existing code.

### L

Liskov Substitution Principle (LSP)

A subtype should work wherever its parent type is expected.

### I

Interface Segregation Principle (ISP)

Don't force classes to implement methods they don't need.

### D

Dependency Inversion Principle (DIP)

Depend on abstractions instead of concrete implementations.

### 1\. S — Single Responsibility Principle

Meaning: A class should have one main reason to change.

Imagine a class that handles an employee's data, calculates salary, and saves the employee to a file. It has three different responsibilities.

### Bad example

```java
class Employee {
    String name;
    double salary;

    void calculateSalary() {
        System.out.println("Calculating salary");
    }

    void saveToFile() {
        System.out.println("Saving employee to file");
    }
}
```

The `Employee` class manages employee data, salary calculation, and file storage.

If the file-saving logic changes, you must modify `Employee`, even though saving files isn't its main responsibility.

### Better example

```java
class Employee {
    String name;
    double salary;
}

class SalaryCalculator {
    void calculate(Employee employee) {
        System.out.println("Calculating salary");
    }
}

class EmployeeRepository {
    void save(Employee employee) {
        System.out.println("Saving employee");
    }
}
```

Now each class has a clear purpose.

* `Employee` — holds employee data.

* `SalaryCalculator` — handles salary calculations.

* `EmployeeRepository` — handles saving employee data.

Remember: One class doesn't necessarily mean one method. It means one cohesive responsibility.

### 2\. O — Open/Closed Principle

Meaning: Your code should be open for extension but closed for modification.

In simple terms, adding a new feature shouldn't require rewriting a class that already works.

### Bad example

Suppose your application supports cash and UPI payments.

```java
class PaymentService {

    void pay(String type, double amount) {
        if (type.equals("cash")) {
            System.out.println("Paid by cash");
        } else if (type.equals("upi")) {
            System.out.println("Paid by UPI");
        }
    }
}
```

Now you want to support card payments. You must modify `PaymentService` and add another `else if`.

As payment types grow, the class becomes harder to maintain.

### Better example

First, define a common interface:

```java
interface Payment {
    void pay(double amount);
}
```

Create implementations:

```java
class CashPayment implements Payment {
    public void pay(double amount) {
        System.out.println("Paid by cash: " + amount);
    }
}

class UpiPayment implements Payment {
    public void pay(double amount) {
        System.out.println("Paid by UPI: " + amount);
    }
}

class CardPayment implements Payment {
    public void pay(double amount) {
        System.out.println("Paid by card: " + amount);
    }
}
```

The service depends on the interface:

```java
class PaymentService {
    void processPayment(Payment payment, double amount) {
        payment.pay(amount);
    }
}
```

Usage:

```java
public class Main {
    public static void main(String[] args) {
        PaymentService service = new PaymentService();

        service.processPayment(new CashPayment(), 500);
        service.processPayment(new UpiPayment(), 1000);
        service.processPayment(new CardPayment(), 2000);
    }
}
```

Output:

```text
Paid by cash: 500.0
Paid by UPI: 1000.0
Paid by card: 2000.0
```

You added `CardPayment` without changing `PaymentService`.

Remember: Use interfaces and polymorphism when new variations of a behavior are likely. You don't need to create an interface for every class.

### 3\. L — Liskov Substitution Principle

Meaning: If class `B` extends class `A`, you should be able to use `B` wherever `A` is expected without breaking the program's expected behavior.

### Bad example

Consider this design:

```java
class Bird {
    void fly() {
        System.out.println("Bird is flying");
    }
}

class Sparrow extends Bird {
}

class Penguin extends Bird {
    @Override
    void fly() {
        throw new UnsupportedOperationException(
            "Penguins cannot fly"
        );
    }
}
```

Now look at this method:

```java
static void makeBirdFly(Bird bird) {
    bird.fly();
}
```

Calling `makeBirdFly(new Penguin())` throws an exception. The method expects a bird that can fly, but the `Bird` abstraction promises flying for every bird.

The inheritance design is wrong.

### Better example

Separate the general concept of a bird from the ability to fly.

```java
class Bird {
    void eat() {
        System.out.println("Bird is eating");
    }
}

interface Flyable {
    void fly();
}

class Sparrow extends Bird implements Flyable {
    public void fly() {
        System.out.println("Sparrow is flying");
    }
}

class Penguin extends Bird {
    void swim() {
        System.out.println("Penguin is swimming");
    }
}
```

Now the flying method accepts only birds that can fly:

```java
static void makeBirdFly(Flyable bird) {
    bird.fly();
}
```

Usage:

```java
makeBirdFly(new Sparrow());
```

This works without pretending a penguin can fly.

Remember: Inheritance should preserve the promises made by the parent type. If a subclass breaks those promises, rethink the hierarchy.

### 4\. I — Interface Segregation Principle

Meaning: A class shouldn't be forced to implement methods it doesn't need.

### Bad example

```java
interface Machine {
    void print();
    void scan();
    void fax();
}
```

A basic printer only prints, but it must implement every method:

```java
class BasicPrinter implements Machine {
    public void print() {
        System.out.println("Printing");
    }

    public void scan() {
        throw new UnsupportedOperationException();
    }

    public void fax() {
        throw new UnsupportedOperationException();
    }
}
```

This is a sign that the interface is too broad.

### Better example

Split the interface into smaller ones:

```java
interface Printable {
    void print();
}

interface Scannable {
    void scan();
}

interface Faxable {
    void fax();
}
```

A basic printer implements only what it supports:

```java
class BasicPrinter implements Printable {
    public void print() {
        System.out.println("Printing");
    }
}
```

An advanced printer can implement multiple interfaces:

```java
class AdvancedPrinter
        implements Printable, Scannable, Faxable {

    public void print() {
        System.out.println("Printing");
    }

    public void scan() {
        System.out.println("Scanning");
    }

    public void fax() {
        System.out.println("Faxing");
    }
}
```

Remember: Prefer several small, focused interfaces over one giant interface when clients need different capabilities.

### 5\. D — Dependency Inversion Principle

Meaning: High-level business logic should depend on abstractions, not directly on low-level concrete classes.

Let's use a notification example.

### Bad example

```java
class EmailService {
    void send(String message) {
        System.out.println("Email: " + message);
    }
}

class OrderService {
    private EmailService emailService = new EmailService();

    void placeOrder() {
        System.out.println("Order placed");
        emailService.send("Your order is placed");
    }
}
```

`OrderService` directly creates `EmailService`. If you want SMS notifications instead, you need to change `OrderService`.

### Better example

Create an abstraction:

```java
interface NotificationService {
    void send(String message);
}
```

Implement it:

```java
class EmailService implements NotificationService {
    public void send(String message) {
        System.out.println("Email: " + message);
    }
}

class SmsService implements NotificationService {
    public void send(String message) {
        System.out.println("SMS: " + message);
    }
}
```

Inject the dependency through the constructor:

```java
class OrderService {
    private final NotificationService notificationService;

    OrderService(NotificationService notificationService) {
        this.notificationService = notificationService;
    }

    void placeOrder() {
        System.out.println("Order placed");
        notificationService.send("Your order is placed");
    }
}
```

Usage:

```java
public class Main {
    public static void main(String[] args) {
        NotificationService notification = new EmailService();

        OrderService orderService = new OrderService(notification);
        orderService.placeOrder();
    }
}
```

To switch to SMS, change the object supplied to the constructor:

```java
NotificationService notification = new SmsService();
OrderService orderService = new OrderService(notification);
```

`OrderService` doesn't need to know which concrete notification implementation it receives.

Remember: Constructor injection is a simple way to implement dependency inversion in plain Java. You don't need Spring to use it.

### SOLID quick revision

| Principle | Main question                                                   | Common solution                       |
| --------- | --------------------------------------------------------------- | ------------------------------------- |
| S — SRP   | Does this class have unrelated responsibilities?                | Split responsibilities                |
| O — OCP   | Do I keep modifying existing code to add variations?            | Interfaces and polymorphism           |
| L — LSP   | Can a subtype replace its parent without breaking expectations? | Correct inheritance and contracts     |
| I — ISP   | Must a class implement methods it doesn't need?                 | Smaller interfaces                    |
| D — DIP   | Is business logic tightly coupled to a concrete class?          | Abstractions and dependency injection |

