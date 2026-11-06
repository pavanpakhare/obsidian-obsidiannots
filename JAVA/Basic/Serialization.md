[[java]]

Here’s a **study-friendly summary of your Serialization PDF**, keeping the terminology and topics from the document.

## Java Serialization — Quick Summary

The PDF covers **Serialization, Deserialization, `transient`, object graphs, customized serialization, inheritance, Externalization, and `serialVersionUID`**.

### 1. Serialization

**Serialization** is the process of converting a Java object into a **file/network-supported form** so its state can be saved.

Main classes:

- `FileOutputStream`
    
- `ObjectOutputStream`
    

Basic flow:

```java
FileOutputStream fos = new FileOutputStream("abc.ser");
ObjectOutputStream oos = new ObjectOutputStream(fos);

oos.writeObject(object);
```

A class must implement `Serializable`:

```java
class Dog implements Serializable {
    int i = 10;
    int j = 20;
}
```

`Serializable` is a **marker interface** in `java.io`; it has no methods.

---

### 2. Deserialization

**Deserialization** reads the saved object and converts it back into a Java object.

Main classes:

- `FileInputStream`
    
- `ObjectInputStream`
    

```java
FileInputStream fis = new FileInputStream("abc.ser");
ObjectInputStream ois = new ObjectInputStream(fis);

Dog d = (Dog) ois.readObject();
```

The objects must generally be read in the **same order in which they were written**.

---

## 3. `Serializable`

```java
class Dog implements Serializable
```

Important points:

- Located in `java.io`.
    
- Marker interface.
    
- Contains no methods.
    
- JVM provides the serialization capability.
    
- Trying to serialize a non-serializable object results in `NotSerializableException`.
    

---

# 4. `transient` Keyword

`transient` can be applied to **variables**.

It means:

> **Do not serialize this variable's value.**

Example:

```java
class Account implements Serializable {
    String username = "Bhaskar";
    transient String password = "kajal";
}
```

After deserialization:

```text
username = Bhaskar
password = null
```

The transient variable receives its **default value** after deserialization.

Typical reason: avoid saving sensitive/unnecessary information.

---

# 5. `static` vs `transient`

A `static` variable belongs to the **class**, not an individual object.

Therefore, it is not part of the object's serialized state.

So:

```java
static transient int x;
```

The `transient` keyword is unnecessary for a static variable.

### Remember

**static → class state**  
**transient → don't serialize object variable**

---

# 6. `final` vs `transient`

According to the PDF, a `final` variable is serialized by its value.

Therefore:

```java
final transient int x = 10;
```

doesn't make the value disappear during serialization.

So declaring a `final` variable as `transient` has no useful effect in the examples discussed.

---

# 7. Multiple Objects

You can serialize multiple objects into the same file:

```java
oos.writeObject(dog);
oos.writeObject(cat);
oos.writeObject(rat);
```

Read them in the same order:

```java
Dog dog = (Dog) ois.readObject();
Cat cat = (Cat) ois.readObject();
Rat rat = (Rat) ois.readObject();
```

If you don't know the type beforehand:

```java
Object obj = ois.readObject();

if (obj instanceof Dog) {
    // Dog logic
}
else if (obj instanceof Cat) {
    // Cat logic
}
```

---

# 8. Object Graph

When an object is serialized, objects **reachable from it** can also be serialized automatically.

Example:

```text
Dog
 ↓
Cat
 ↓
Rat
```

If:

```java
class Dog implements Serializable {
    Cat c;
}

class Cat implements Serializable {
    Rat r;
}

class Rat implements Serializable {
    int j = 20;
}
```

Serializing `Dog` also serializes `Cat` and `Rat`.

This group of reachable objects is called the **object graph**.

### Important

Every object involved in the object graph must be serializable. Otherwise:

```text
NotSerializableException
```

---

# 9. Customized Serialization

Problem:

```java
transient String password;
```

The password isn't serialized, so its information is lost.

The PDF explains **customized serialization** using:

```java
private void writeObject(ObjectOutputStream out)
```

and

```java
private void readObject(ObjectInputStream in)
```

### `writeObject()`

Called automatically during serialization.

Used when you want to perform additional/custom work before saving the object.

### `readObject()`

Called automatically during deserialization.

Used to perform additional/custom work while restoring the object.

Typical pattern:

```java
private void writeObject(ObjectOutputStream out) throws Exception {
    out.defaultWriteObject();

    // custom data
    out.writeObject(customValue);
}

private void readObject(ObjectInputStream in) throws Exception {
    in.defaultReadObject();

    // restore custom data
    customValue = (String) in.readObject();
}
```

---

# 10. Serialization + Inheritance

### Case 1: Parent implements `Serializable`

```java
class Animal implements Serializable {
}

class Dog extends Animal {
}
```

`Dog` is also serializable even though it doesn't explicitly implement `Serializable`.

Serialization capability is inherited from the parent.

---

### Case 2: Parent is NOT Serializable

```java
class Animal {
    int i = 10;
}

class Dog extends Animal implements Serializable {
    int j = 20;
}
```

`Dog` can still be serialized.

But the state coming from the non-serializable parent is **not serialized**.

During deserialization, the parent portion is initialized using the **no-argument constructor** of the non-serializable parent.

Therefore, the non-serializable parent should have an accessible no-arg constructor; otherwise the PDF states that `InvalidClassException` can occur.

Example result from the PDF:

```text
Before serialization:
i = 888
j = 999

After deserialization:
i = 10
j = 999
```

---

# 11. Externalization

**Externalization** gives the programmer more control over what gets saved.

Class must implement:

```java
Externalizable
```

It provides two methods:

```java
writeExternal()
readExternal()
```

Example:

```java
class Employee implements Externalizable {

    String name;
    int age;
    int salary;

    public void writeExternal(ObjectOutput out)
            throws IOException {

        out.writeObject(name);
        out.writeInt(age);
    }

    public void readExternal(ObjectInput in)
            throws IOException, ClassNotFoundException {

        name = (String) in.readObject();
        age = in.readInt();
    }
}
```

Here, you can choose which fields to save.

### Important requirement

An `Externalizable` class must have a **public no-argument constructor**.

Otherwise:

```text
InvalidClassException
```

can occur during deserialization.

---

# 12. Serialization vs Externalization

|Serialization|Externalization|
|---|---|
|Default serialization|Customized serialization|
|JVM handles serialization|Programmer controls serialization|
|Entire object state is handled|Programmer chooses what to save|
|`Serializable`|`Externalizable`|
|Marker interface|Not a marker interface|
|No methods|`writeExternal()` + `readExternal()`|
|No public no-arg constructor requirement|Public no-arg constructor required|
|`transient` has a role|`transient` doesn't control what is written|
|Less control|More control|

---

# 13. `serialVersionUID`

`serialVersionUID` is a unique identifier used during serialization/deserialization.

Example:

```java
class Dog implements Serializable {

    private static final long serialVersionUID = 1L;

    int i = 10;
    int j = 20;
}
```

During deserialization, the JVM compares the stored `serialVersionUID` with the class's current `serialVersionUID`.

If they don't match:

```text
InvalidClassException
```

### Why explicitly declare it?

Instead of allowing the JVM to calculate one automatically:

```java
private static final long serialVersionUID = 1L;
```

This provides a stable identifier across compatible class changes. The PDF also discusses JVM-version compatibility and recommends configuring your own value.

---

# ⭐ Exam Revision — Remember These

```text
Serialization
     ↓
Object → File/Network form
     ↓
ObjectOutputStream
```

```text
Deserialization
     ↓
File/Network form → Object
     ↓
ObjectInputStream
```

### Key keywords

```text
Serializable
    ↓
Marker interface

transient
    ↓
Don't serialize variable

static
    ↓
Not part of object state

Externalizable
    ↓
Programmer controls serialization

writeObject()
    ↓
Customize serialization

readObject()
    ↓
Customize deserialization

serialVersionUID
    ↓
Version/compatibility identifier
```

### Most important exceptions

- **`NotSerializableException`** → attempting to serialize a non-serializable object.
    
- **`InvalidClassException`** → commonly associated in the PDF with `serialVersionUID` mismatch or missing required constructor for externalization/non-serializable parent cases.
    

**One-line definition to remember:**

> **Serialization = converting an object's state into a storable/transmittable form; Deserialization = reconstructing the object from that form.**