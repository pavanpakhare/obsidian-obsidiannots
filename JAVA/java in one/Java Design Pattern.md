# **Design Patterns in Java – Complete Tutorial with Examples**

Design patterns are **reusable solutions** to common software design problems. They help in:
✅ Writing **clean, maintainable, and scalable** code  
✅ Following **proven best practices**  
✅ Improving **team collaboration**  

This tutorial covers **23 Gang of Four (GoF) patterns** + **modern patterns** with Java examples.

---

## **Table of Contents**
### **1. Creational Patterns** (Object Creation)
- [Singleton](#1-singleton)
- [Factory Method](#2-factory-method)
- [Abstract Factory](#3-abstract-factory)
- [Builder](#4-builder)
- [Prototype](#5-prototype)

### **2. Structural Patterns** (Object Composition)
- [Adapter](#6-adapter)
- [Bridge](#7-bridge)
- [Composite](#8-composite)
- [Decorator](#9-decorator)
- [Facade](#10-facade)
- [Flyweight](#11-flyweight)
- [Proxy](#12-proxy)

### **3. Behavioral Patterns** (Object Interaction)
- [Chain of Responsibility](#13-chain-of-responsibility)
- [Command](#14-command)
- [Iterator](#15-iterator)
- [Mediator](#16-mediator)
- [Memento](#17-memento)
- [Observer](#18-observer)
- [State](#19-state)
- [Strategy](#20-strategy)
- [Template Method](#21-template-method)
- [Visitor](#22-visitor)

### **4. Modern Patterns**
- [Dependency Injection](#23-dependency-injection)
- [Repository](#24-repository)
- [Model-View-Controller (MVC)](#25-model-view-controller-mvc)

---

## **1. Singleton**
Ensures **only one instance** of a class exists.

### **Example: Thread-Safe Singleton**
```java
public class Database {
    private static volatile Database instance;

    private Database() {} // Private constructor

    public static Database getInstance() {
        if (instance == null) {
            synchronized (Database.class) {
                if (instance == null) {
                    instance = new Database();
                }
            }
        }
        return instance;
    }
}
```
**Usage:**
```java
Database db = Database.getInstance();
```

---

## **2. Factory Method**
Defines an **interface for object creation**, letting subclasses decide which class to instantiate.

### **Example: Payment Processor**
```java
interface Payment {
    void pay(int amount);
}

class CreditCardPayment implements Payment {
    public void pay(int amount) {
        System.out.println("Paid " + amount + " via Credit Card");
    }
}

class PayPalPayment implements Payment {
    public void pay(int amount) {
        System.out.println("Paid " + amount + " via PayPal");
    }
}

class PaymentFactory {
    public static Payment createPayment(String type) {
        return switch (type.toLowerCase()) {
            case "creditcard" -> new CreditCardPayment();
            case "paypal" -> new PayPalPayment();
            default -> throw new IllegalArgumentException("Unknown payment type");
        };
    }
}
```
**Usage:**
```java
Payment payment = PaymentFactory.createPayment("creditcard");
payment.pay(100);
```

---

## **3. Abstract Factory**
Provides an **interface for creating families of related objects** without specifying concrete classes.

### **Example: UI Components**
```java
interface Button {
    void render();
}

interface Checkbox {
    void check();
}

// Windows UI
class WindowsButton implements Button {
    public void render() { System.out.println("Windows Button"); }
}
class WindowsCheckbox implements Checkbox {
    public void check() { System.out.println("Windows Checkbox"); }
}

// Mac UI
class MacButton implements Button {
    public void render() { System.out.println("Mac Button"); }
}
class MacCheckbox implements Checkbox {
    public void check() { System.out.println("Mac Checkbox"); }
}

// Abstract Factory
interface GUIFactory {
    Button createButton();
    Checkbox createCheckbox();
}

class WindowsFactory implements GUIFactory {
    public Button createButton() { return new WindowsButton(); }
    public Checkbox createCheckbox() { return new WindowsCheckbox(); }
}

class MacFactory implements GUIFactory {
    public Button createButton() { return new MacButton(); }
    public Checkbox createCheckbox() { return new MacCheckbox(); }
}
```
**Usage:**
```java
GUIFactory factory = new MacFactory();
Button button = factory.createButton();
button.render(); // "Mac Button"
```

---

## **4. Builder**
Constructs **complex objects step by step**.

### **Example: Building a Computer**
```java
class Computer {
    private String CPU;
    private String RAM;
    private String storage;

    private Computer(Builder builder) {
        this.CPU = builder.CPU;
        this.RAM = builder.RAM;
        this.storage = builder.storage;
    }

    public static class Builder {
        private String CPU;
        private String RAM;
        private String storage;

        public Builder setCPU(String CPU) { this.CPU = CPU; return this; }
        public Builder setRAM(String RAM) { this.RAM = RAM; return this; }
        public Builder setStorage(String storage) { this.storage = storage; return this; }
        public Computer build() { return new Computer(this); }
    }
}
```
**Usage:**
```java
Computer gamingPC = new Computer.Builder()
    .setCPU("Intel i9")
    .setRAM("32GB")
    .setStorage("1TB SSD")
    .build();
```

---

## **5. Prototype**
Creates **clones of existing objects** (instead of new instances).

### **Example: Cloning Shapes**
```java
interface Shape extends Cloneable {
    Shape clone();
    void draw();
}

class Circle implements Shape {
    public Shape clone() {
        return new Circle();
    }
    public void draw() {
        System.out.println("Drawing Circle");
    }
}
```
**Usage:**
```java
Shape circle = new Circle();
Shape clonedCircle = circle.clone();
clonedCircle.draw(); // "Drawing Circle"
```

---

## **6. Adapter**
Converts the **interface of a class** into another interface clients expect.

### **Example: Legacy Payment Adapter**
```java
class LegacyPayment {
    public void payInDollars(int amount) {
        System.out.println("Paid $" + amount);
    }
}

interface ModernPayment {
    void pay(int amount);
}

class PaymentAdapter implements ModernPayment {
    private LegacyPayment legacyPayment;

    public PaymentAdapter(LegacyPayment legacyPayment) {
        this.legacyPayment = legacyPayment;
    }

    public void pay(int amount) {
        legacyPayment.payInDollars(amount);
    }
}
```
**Usage:**
```java
ModernPayment payment = new PaymentAdapter(new LegacyPayment());
payment.pay(50); // "Paid $50"
```

---

## **7. Bridge**
Decouples **abstraction from implementation**.

### **Example: Remote Control for Devices**
```java
interface Device {
    void turnOn();
    void turnOff();
}

class TV implements Device {
    public void turnOn() { System.out.println("TV is ON"); }
    public void turnOff() { System.out.println("TV is OFF"); }
}

abstract class RemoteControl {
    protected Device device;
    public RemoteControl(Device device) { this.device = device; }
    abstract void power();
}

class BasicRemote extends RemoteControl {
    public BasicRemote(Device device) { super(device); }
    public void power() { device.turnOn(); }
}
```
**Usage:**
```java
RemoteControl remote = new BasicRemote(new TV());
remote.power(); // "TV is ON"
```

---

## **8. Composite**
Treats **individual and group objects uniformly**.

### **Example: File System**
```java
interface FileSystemComponent {
    void showDetails();
}

class File implements FileSystemComponent {
    private String name;
    public File(String name) { this.name = name; }
    public void showDetails() { System.out.println("File: " + name); }
}

class Directory implements FileSystemComponent {
    private List<FileSystemComponent> files = new ArrayList<>();
    public void add(FileSystemComponent component) { files.add(component); }
    public void showDetails() {
        for (FileSystemComponent file : files) {
            file.showDetails();
        }
    }
}
```
**Usage:**
```java
Directory root = new Directory();
root.add(new File("file1.txt"));
root.add(new File("file2.txt"));
root.showDetails();
```

---

## **9. Decorator**
Adds **new behaviors dynamically** without altering existing code.

### **Example: Coffee Decorators**
```java
interface Coffee {
    double getCost();
    String getDescription();
}

class SimpleCoffee implements Coffee {
    public double getCost() { return 2.0; }
    public String getDescription() { return "Simple Coffee"; }
}

class MilkDecorator implements Coffee {
    private Coffee coffee;
    public MilkDecorator(Coffee coffee) { this.coffee = coffee; }
    public double getCost() { return coffee.getCost() + 0.5; }
    public String getDescription() { return coffee.getDescription() + ", Milk"; }
}
```
**Usage:**
```java
Coffee coffee = new MilkDecorator(new SimpleCoffee());
System.out.println(coffee.getDescription()); // "Simple Coffee, Milk"
```

---

## **10. Facade**
Provides a **simplified interface** to a complex system.

### **Example: Home Theater System**
```java
class SoundSystem {
    void on() { System.out.println("Sound ON"); }
}

class Projector {
    void on() { System.out.println("Projector ON"); }
}

class HomeTheaterFacade {
    private SoundSystem sound;
    private Projector projector;

    public HomeTheaterFacade() {
        sound = new SoundSystem();
        projector = new Projector();
    }

    public void watchMovie() {
        sound.on();
        projector.on();
    }
}
```
**Usage:**
```java
HomeTheaterFacade theater = new HomeTheaterFacade();
theater.watchMovie();
```

---

## **11. Flyweight**
Minimizes **memory usage** by sharing data.

### **Example: Game Trees**
```java
class TreeType {
    private String name;
    private String color;
    public TreeType(String name, String color) { this.name = name; this.color = color; }
    public void draw(int x, int y) {
        System.out.println("Drawing " + name + " at (" + x + "," + y + ")");
    }
}

class TreeFactory {
    private static Map<String, TreeType> treeTypes = new HashMap<>();
    public static TreeType getTreeType(String name, String color) {
        String key = name + "-" + color;
        if (!treeTypes.containsKey(key)) {
            treeTypes.put(key, new TreeType(name, color));
        }
        return treeTypes.get(key);
    }
}
```
**Usage:**
```java
TreeType pine = TreeFactory.getTreeType("Pine", "Green");
pine.draw(10, 20);
```

---

## **12. Proxy**
Provides a **surrogate** for another object.

### **Example: Lazy Loading**
```java
interface Image {
    void display();
}

class RealImage implements Image {
    private String filename;
    public RealImage(String filename) {
        this.filename = filename;
        loadFromDisk();
    }
    private void loadFromDisk() { System.out.println("Loading " + filename); }
    public void display() { System.out.println("Displaying " + filename); }
}

class ProxyImage implements Image {
    private RealImage realImage;
    private String filename;
    public ProxyImage(String filename) { this.filename = filename; }
    public void display() {
        if (realImage == null) {
            realImage = new RealImage(filename);
        }
        realImage.display();
    }
}
```
**Usage:**
```java
Image image = new ProxyImage("photo.jpg");
image.display(); // Loads and displays
```