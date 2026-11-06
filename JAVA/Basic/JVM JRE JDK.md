[[java]]

## 1. What is JVM?

**JVM = Java Virtual Machine**

When you write:

```java
public class Main {
    public static void main(String[] args) {
        System.out.println("Hello");
    }
}
```

The process is:

```text
Main.java
   ↓ javac
Main.class
   ↓
Bytecode
   ↓
JVM
   ↓
Machine Code
   ↓
CPU
```

The important point:

> Java source code is compiled into bytecode, and the JVM executes that bytecode.

---

# 2. JDK vs JRE vs JVM

This is one of the most common interview questions.

```text
JDK
 ├── Development Tools
 │    ├── javac
 │    ├── java
 │    └── javadoc
 │
 └── JRE
      ├── JVM
      └── Java Libraries
```

### JVM

Runs Java bytecode.

### JRE

Provides the environment required to **run** Java applications.

```text
JRE = JVM + Java Libraries
```

### JDK

Used to **develop and run** Java applications.

```text
JDK = JRE + Development Tools
```

### Interview answer

> JVM executes bytecode, JRE provides the runtime environment containing JVM and libraries, while JDK provides the tools required for Java development along with the runtime environment.

---

# 3. JVM Architecture

Know this diagram for interviews:

```text
                 JVM
                  │
      ┌───────────┴───────────┐
      │                       │
 Class Loader              Runtime Data Areas
      │                       │
      │              ┌────────┼─────────┐
      │              │        │         │
      │           Heap      Stack    Method Area
      │
      │
 Execution Engine
      │
 ┌────┴─────────┐
 │              │
Interpreter     JIT Compiler
 │              │
 └──────┬───────┘
        │
   Native Method
     Interface
        │
   Native Libraries
```

---

# 4. Class Loader

The **Class Loader** loads `.class` files into JVM memory.

For example:

```java
Student.class
```

is loaded when the JVM needs the `Student` class.

### Main class-loader components

Modern JVMs commonly have:

```text
Bootstrap ClassLoader
        ↓
Platform ClassLoader
        ↓
Application ClassLoader
```

### Bootstrap ClassLoader

Loads core Java classes such as:

```java
java.lang.String
java.lang.Object
```

### Platform ClassLoader

Loads Java platform classes outside the core modules.

### Application ClassLoader

Loads classes from the application's classpath.

---

# 5. Class Loading Process

A common interview question.

Class loading broadly involves:

```text
Loading
   ↓
Linking
   ├── Verification
   ├── Preparation
   └── Resolution
   ↓
Initialization
```

### Loading

JVM finds the class and creates its internal representation.

### Verification

Checks whether bytecode is valid and safe.

### Preparation

Allocates memory for class-level/static fields and gives default values.

### Resolution

Converts symbolic references into direct references when required.

### Initialization

Static initialization occurs.

Example:

```java
class Test {

    static int x = 10;

    static {
        System.out.println("Hello");
    }
}
```

Initialization executes the relevant static initialization.

---

# 6. JVM Memory Areas

Very important for interviews.

```text
JVM Memory
│
├── Heap
├── Stack
├── Method Area
├── PC Register
└── Native Method Stack
```

---

## 7. Heap

The **Heap** stores objects.

Example:

```java
Student s = new Student();
```

The `Student` object is created in the heap.

Conceptually:

```text
Stack                    Heap

s ───────────────────→ Student Object
                         name
                         age
```

Heap is also the main memory area managed by the **Garbage Collector**.

---

# 8. Stack

Each thread has its own JVM stack.

Example:

```java
public static void main(String[] args) {
    int x = 10;
    test();
}

static void test() {
    int y = 20;
}
```

Conceptually:

```text
Thread Stack

┌──────────────┐
│ test()       │
│ y = 20       │
├──────────────┤
│ main()       │
│ x = 10       │
└──────────────┘
```

Each method invocation creates a **stack frame**.

A stack frame contains things such as:

- Local variables
    
- Operand stack
    
- Reference information
    
- Return information
    

---

# 9. Heap vs Stack

Very common interview question.

|Heap|Stack|
|---|---|
|Objects are stored|Method frames/local execution data|
|Shared between threads|Each thread has its own stack|
|Garbage collected|Frames removed when methods return|
|Generally larger|Generally smaller|
|`new Student()` object|Local variables/references are associated with frames|

Important nuance:

> Don't say "all primitive variables are always stored in stack." Their storage depends on context and JVM implementation. For interview purposes, local primitive variables are commonly described as being in the stack frame.

---

# 10. Method Area

The JVM has a **method area** for class-level information.

It contains information such as:

- Class metadata
    
- Method metadata
    
- Runtime constant pool
    
- Information related to fields/methods
    

In modern HotSpot JVMs, the implementation of the method area is associated with **Metaspace**, which is native memory rather than the Java heap.

Interview trap:

> **Method Area is a JVM specification concept; Metaspace is a HotSpot implementation detail.**

---

# 11. PC Register

PC means **Program Counter**.

Each thread has its own PC register.

It keeps track of the instruction currently being executed/next instruction for that thread.

---

# 12. Native Method Stack

Used for execution of **native methods** written in languages such as C/C++.

Example:

```java
public native void someMethod();
```

Such methods can interact with native libraries through JNI.

---

# 13. Execution Engine

After bytecode is loaded, the **Execution Engine** executes it.

Main components:

```text
Execution Engine
      │
      ├── Interpreter
      │
      ├── JIT Compiler
      │
      └── Garbage Collector
```

---

# 14. Interpreter

The interpreter executes bytecode instruction by instruction.

Example conceptually:

```text
Bytecode
   ↓
Instruction 1 → execute
Instruction 2 → execute
Instruction 3 → execute
```

Advantage:

- Fast startup
    

Disadvantage:

- Repeatedly interpreting frequently executed code can be slower.
    

---

# 15. JIT Compiler

**JIT = Just-In-Time Compiler**

It improves performance by compiling frequently executed bytecode into native machine code at runtime.

```text
Bytecode
   ↓
JIT Compiler
   ↓
Native Machine Code
   ↓
CPU
```

Example:

```java
for (int i = 0; i < 1_000_000; i++) {
    calculate();
}
```

If some code becomes "hot" through repeated execution, the JVM can optimize/compile it.

### Interview answer

> The interpreter executes bytecode directly, while the JIT compiler identifies frequently executed code and compiles it into optimized native machine code to improve performance.

---

# 16. What is HotSpot?

**HotSpot** is a popular JVM implementation from OpenJDK.

It uses techniques such as:

- JIT compilation
    
- Runtime optimization
    
- Garbage collection
    

The important distinction:

```text
Java
 ↓
JVM specification
 ↓
HotSpot / other JVM implementations
```

---

# 17. Garbage Collection

Garbage Collection automatically removes objects that are no longer reachable.

Example:

```java
Student s = new Student();

s = null;
```

The `Student` object may become eligible for garbage collection if no reachable references remain.

```text
Before:

s ───→ Student Object


After:

s ───→ null

Student Object
      ↓
Eligible for GC
```

Important:

> `System.gc()` does not guarantee that garbage collection will happen immediately.

---

# 18. What is a Memory Leak in Java?

Even though Java has Garbage Collection, memory leaks can still happen.

Example:

```java
static List<Object> list = new ArrayList<>();

public void addObject() {
    list.add(new Object());
}
```

If objects remain reachable through `list` even though the application no longer needs them, GC cannot reclaim them.

So:

> Garbage collection removes unreachable objects, not objects that are simply "unused" from the programmer's perspective.

---

# 19. StackOverflowError vs OutOfMemoryError

Very common interview question.

### StackOverflowError

Usually occurs when the thread's stack is exhausted.

Example:

```java
static void test() {
    test();
}
```

Infinite recursion:

```text
test()
 ↓
test()
 ↓
test()
 ↓
...
 ↓
StackOverflowError
```

### OutOfMemoryError

Occurs when JVM cannot allocate required memory.

Example:

```java
List<byte[]> list = new ArrayList<>();

while (true) {
    list.add(new byte[1024 * 1024]);
}
```

Eventually memory may be exhausted.

---

# 20. JVM Execution Flow

You should be able to explain this in an interview:

```text
.java
  │
  │ javac
  ↓
.class
  │
  ↓
Class Loader
  │
  ↓
Bytecode Verification
  │
  ↓
Runtime Memory
  │
  ├── Heap
  ├── Stack
  ├── Method Area
  ├── PC Register
  └── Native Stack
  │
  ↓
Execution Engine
  │
  ├── Interpreter
  └── JIT
  │
  ↓
Machine Code
  │
  ↓
CPU
```

---

# ⭐ Most Important JVM Interview Questions

Prepare these especially well:

### Basic

1. What is JVM?
    
2. What is the difference between JDK, JRE and JVM?
    
3. Why is Java platform independent?
    
4. Is JVM platform independent?
    
5. What is bytecode?
    
6. What happens when you run a Java program?
    

### JVM Architecture

7. Explain JVM architecture.
    
8. What is a ClassLoader?
    
9. What are the different ClassLoaders?
    
10. What is the class-loading process?
    
11. What is the Method Area?
    
12. What is Metaspace?
    
13. What is the PC Register?
    
14. What is Native Method Stack?
    

### Memory

15. What is Heap memory?
    
16. What is Stack memory?
    
17. Heap vs Stack?
    
18. Where are objects stored?
    
19. Where are local variables stored?
    
20. What is a stack frame?
    
21. What causes StackOverflowError?
    
22. What causes OutOfMemoryError?
    

### Execution

23. What is the interpreter?
    
24. What is JIT?
    
25. Why does JVM use JIT?
    
26. What is HotSpot?
    
27. What is native machine code?
    

### Garbage Collection

28. What is Garbage Collection?
    
29. How does GC identify objects for collection?
    
30. Can you force Garbage Collection?
    
31. What is `System.gc()`?
    
32. Can Java have memory leaks?
    
33. What is the difference between memory leak and memory overflow?
    

---

## 🔥 5 Interview Questions You Should Be Able to Answer

**Q1. Why is Java platform independent?**

> Java source code is compiled into platform-independent bytecode. A JVM implementation for each operating system executes that bytecode, allowing the same `.class`/JAR application to run across platforms.

**Q2. Is JVM platform independent?**

> No. JVM implementations are platform-specific. Java bytecode is platform-independent, while the JVM that executes it is implemented for a particular operating system and architecture.

**Q3. Where are objects stored?**

> Objects are generally allocated on the heap, while references and local variables are represented within stack frames when they are local to a method. Exact physical placement is an implementation detail of the JVM.

**Q4. What is JIT?**

> JIT is a runtime compiler that compiles frequently executed bytecode into native machine code and applies optimizations to improve execution performance.

**Q5. What happens when `main()` starts?**

> The JVM loads the required classes, initializes the relevant class, creates the main thread and its stack frame, and then executes the `main()` method through the JVM execution engine.