[[java]]
If you mean **“What is the `bin` directory provided by the JDK?”**, here’s the structure:

```text
JDK/
├── bin/
├── conf/
├── include/
├── jmods/
├── legal/
└── lib/
```

### `JDK/bin`

The `bin` directory contains **command-line tools/executables provided by the JDK**.

| Command     | Purpose                                |
| ----------- | -------------------------------------- |
| `java`      | Runs Java applications                 |
| `javac`     | Compiles `.java` → `.class`            |
| `jar`       | Creates/manages JAR files              |
| `javadoc`   | Generates API documentation            |
| `jdb`       | Java debugger                          |
| `javap`     | Examines `.class` files                |
| `jshell`    | Interactive Java shell                 |
| `jlink`     | Creates custom Java runtime images     |
| `jpackage`  | Packages Java applications             |
| `jconsole`  | GUI monitoring/management tool         |
| `jcmd`      | Sends diagnostic commands to JVM       |
| `jps`       | Lists running JVM processes            |
| `jstack`    | Gets thread stack information          |
| `jmap`      | Gets JVM memory/heap information       |
| `jinfo`     | Displays JVM configuration information |
| `jstat`     | JVM performance statistics             |
| `jdeps`     | Analyzes Java class/JAR dependencies   |
| `keytool`   | Manages keys/certificates              |
| `jarsigner` | Signs/verifies JAR files               |

### Example

When you run:

```bash
javac Hello.java
```

your operating system finds `javac` inside:

```text
JDK/bin/javac
```

That's why Java installation instructions usually add:

```text
JDK/bin
```

to the **PATH**.

For example:

```bash
export PATH="$JAVA_HOME/bin:$PATH"
```

Then commands such as:

```bash
java
javac
jar
jshell
javadoc
```

can be executed from any directory.

**Important:** `bin` contains the **tools**, while the JDK's `lib`, `jmods`, `conf`, etc. contain supporting runtime, module, configuration, and other files.

Yes. The JDK `bin` directory is basically a **toolbox for developing, running, debugging, inspecting, packaging, and diagnosing Java applications**.

Here is a practical tutorial, from beginner to advanced.

# JDK `bin` Tools — Complete Practical Guide

First check your JDK:

```bash
java -version
javac -version
```

Find where the tools are installed:

```bash
which java
which javac
```

On Linux:

```bash
ls "$JAVA_HOME/bin"
```

---

## 1. `java` — Run Java programs

You use `java` to **run compiled Java applications**.

Create:

```java
// Hello.java
public class Hello {
    public static void main(String[] args) {
        System.out.println("Hello Java");
    }
}
```

Compile:

```bash
javac Hello.java
```

Run:

```bash
java Hello
```

Output:

```text
Hello Java
```

You can also pass arguments:

```bash
java Hello Pavan Java
```

```java
public static void main(String[] args) {
    for (String arg : args) {
        System.out.println(arg);
    }
}
```

---

# 2. `javac` — Java compiler

`javac` converts:

```text
.java
  ↓
.class
```

Example:

```bash
javac Hello.java
```

You can specify the output directory:

```bash
javac -d out Hello.java
```

Result:

```text
out/
└── Hello.class
```

Compile multiple files:

```bash
javac *.java
```

Compile with dependencies/classpath:

```bash
javac -cp lib/example.jar MyApp.java
```

Useful:

```bash
javac --help
```

---

# 3. `jar` — Create JAR files

A JAR is basically a Java application/library archive.

Suppose:

```text
out/
└── Hello.class
```

Create JAR:

```bash
jar --create --file hello.jar -C out .
```

Run it:

```bash
java -cp hello.jar Hello
```

You can inspect it:

```bash
jar --list --file hello.jar
```

Example output:

```text
META-INF/
META-INF/MANIFEST.MF
Hello.class
```

### Executable JAR

Create a manifest:

```text
Main-Class: Hello
```

Then:

```bash
jar --create \
    --file hello.jar \
    --main-class Hello \
    -C out .
```

Run:

```bash
java -jar hello.jar
```

This is particularly important for **Spring Boot**, although Spring Boot's Maven/Gradle plugins normally create the executable JAR for you.

---

# 4. `jshell` — Interactive Java

`jshell` lets you experiment with Java without creating a `.java` file.

Run:

```bash
jshell
```

Then:

```java
int x = 10;
```

```java
int y = 20;
```

```java
x + y
```

Output:

```text
$3 ==> 30
```

Try:

```java
String name = "Pavan";
System.out.println(name);
```

Exit:

```text
/exit
```

Useful commands:

```text
/help
/vars
/methods
/imports
/types
/reset
/exit
```

Very useful when learning Java APIs.

---

# 5. `javadoc` — Generate documentation

Suppose:

```java
/**
 * Calculates the sum of two numbers.
 *
 * @param a first number
 * @param b second number
 * @return sum
 */
public int add(int a, int b) {
    return a + b;
}
```

Generate documentation:

```bash
javadoc Calculator.java
```

You can specify output:

```bash
javadoc -d docs Calculator.java
```

Then:

```text
docs/
├── index.html
├── Calculator.html
└── ...
```

This is how Java API documentation can be generated from source-code comments.

---

# 6. `javap` — Inspect `.class` files

This is one of the **most useful tools for understanding how Java works internally**.

Compile:

```bash
javac Hello.java
```

Then:

```bash
javap Hello
```

Output might be:

```text
Compiled from "Hello.java"
public class Hello {
    public Hello();
    public static void main(java.lang.String[]);
}
```

Get bytecode:

```bash
javap -c Hello
```

You may see:

```text
0: getstatic
3: ldc
5: invokevirtual
...
```

Get more information:

```bash
javap -p Hello
```

```bash
javap -c -p Hello
```

Useful for learning:

* JVM bytecode
* private members
* compiler behavior
* Java internals
* class structure

---

# 7. `jdb` — Java debugger

`jdb` is the command-line Java debugger.

Compile with debugging information:

```bash
javac -g Hello.java
```

Start:

```bash
jdb Hello
```

Set a breakpoint:

```text
stop at Hello:5
```

Run:

```text
run
```

Step:

```text
next
```

Continue:

```text
cont
```

Print variable:

```text
print variableName
```

Exit:

```text
exit
```

In normal development, IDE debuggers such as IntelliJ IDEA or VS Code are generally easier, but `jdb` is useful for understanding Java debugging.

---

# 8. `jps` — List Java processes

Start a Java application.

Then:

```bash
jps
```

Example:

```text
12345 Hello
12567 Jps
```

You get:

```text
PID    Application
```

More information:

```bash
jps -l
```

This becomes useful when working with JVM diagnostics.

---

# 9. `jcmd` — JVM diagnostic tool

`jcmd` is extremely useful for inspecting a running JVM.

First:

```bash
jps
```

Suppose:

```text
12345 MyApplication
```

Then:

```bash
jcmd 12345 VM.version
```

You can see JVM information.

List available commands:

```bash
jcmd 12345 help
```

For example:

```bash
jcmd 12345 VM.flags
```

Get system properties:

```bash
jcmd 12345 VM.system_properties
```

Get thread information:

```bash
jcmd 12345 Thread.print
```

This is especially useful for diagnosing **Spring Boot applications**.

---

# 10. `jstack` — Thread dump

`jstack` shows what JVM threads are doing.

Find PID:

```bash
jps
```

Then:

```bash
jstack 12345
```

You'll see threads such as:

```text
"main"
"Reference Handler"
"Finalizer"
"pool-1-thread-1"
```

This is useful when an application appears:

* frozen
* deadlocked
* CPU-heavy
* stuck waiting

Example:

```bash
jstack 12345 > thread-dump.txt
```

---

# 11. `jmap` — Memory/heap information

`jmap` provides JVM memory information.

For example:

```bash
jmap -heap 12345
```

Depending on the JDK version and JVM, some older `jmap` options may be unavailable or discouraged; `jcmd` is often the preferred modern diagnostic interface.

For a heap dump:

```bash
jmap -dump:format=b,file=heap.hprof 12345
```

Then you can analyze:

```text
heap.hprof
```

using tools such as Eclipse Memory Analyzer.

**Be careful:** heap dumps can be large and can contain sensitive application data.

---

# 12. `jstat` — JVM statistics

`jstat` monitors JVM performance.

Example:

```bash
jstat -gc 12345
```

You might see information about:

```text
S0C
S1C
EC
OC
YGC
YGCT
FGC
FGCT
```

You can monitor repeatedly:

```bash
jstat -gc 12345 1000
```

Meaning:

```text
1000 ms = every 1 second
```

Useful for investigating:

* garbage collection
* heap usage
* GC frequency

---

# 13. `jinfo` — JVM configuration

Example:

```bash
jinfo 12345
```

You can inspect JVM flags/system properties.

However, for many modern diagnostic tasks, prefer:

```bash
jcmd 12345 VM.flags
```

and:

```bash
jcmd 12345 VM.system_properties
```

---

# 14. `jconsole` — GUI JVM monitoring

Run:

```bash
jconsole
```

You'll get a GUI.

It can show:

```text
Memory
Threads
Classes
CPU
MBeans
```

You can connect to a local Java process and monitor it.

For learning JVM monitoring, this is a useful tool.

---

# 15. `jlink` — Create a custom Java runtime

Normally installing a JDK gives you a large set of Java modules.

`jlink` allows you to create a **smaller custom runtime** containing only the modules your application needs.

For example:

```bash
jlink \
  --add-modules java.base,java.sql \
  --output myruntime
```

You'll get:

```text
myruntime/
├── bin/
├── conf/
├── legal/
└── lib/
```

Then:

```bash
./myruntime/bin/java -version
```

This is useful for application distribution.

---

# 16. `jpackage` — Package Java applications

`jpackage` creates native application packages.

For example, depending on your OS:

```text
.deb
.rpm
.msi
.pkg
.app-image
```

Basic example:

```bash
jpackage \
  --input input \
  --name MyApp \
  --main-jar myapp.jar
```

On Fedora/Linux, you can potentially create an RPM:

```bash
jpackage \
  --type rpm \
  --input input \
  --name MyApp \
  --main-jar myapp.jar
```

This is useful when you want to distribute a Java application like a normal desktop application.

---

# 17. `jdeps` — Analyze dependencies

Suppose:

```text
myapp.jar
```

Run:

```bash
jdeps myapp.jar
```

It tells you which Java packages/modules your application depends on.

For example:

```text
myapp.jar -> java.base
myapp.jar -> java.sql
```

This is especially useful with:

```text
jlink
```

because you can determine which modules your application needs.

---

# 18. `keytool` — Certificates and keys

`keytool` manages Java keystores.

Create a key:

```bash
keytool -genkeypair \
  -alias mykey \
  -keyalg RSA \
  -keystore mykeystore.p12 \
  -storetype PKCS12
```

List contents:

```bash
keytool -list -keystore mykeystore.p12
```

This is very important for:

* HTTPS
* TLS
* certificates
* Java applications
* Spring Boot SSL configuration

For example, Spring Boot can use a PKCS12 keystore for HTTPS.

---

# 19. `jarsigner` — Sign JAR files

Sign a JAR:

```bash
jarsigner myapp.jar mykey
```

Verify:

```bash
jarsigner -verify myapp.jar
```

It is used for signing and verifying JAR files.

---

# 20. `serialver`

For classes implementing `Serializable`:

```bash
serialver MyClass
```

It can generate a `serialVersionUID`.

Example:

```text
MyClass:    private static final long serialVersionUID = ...
```

It's mainly useful when working with Java serialization.

---

# 21. `javaw`

On Windows, `javaw` can launch a Java GUI application **without opening a console window**.

For example:

```cmd
javaw -jar MyApp.jar
```

It's primarily relevant to Windows.

---

# 22. `rmiregistry`

Used with Java RMI:

```bash
rmiregistry
```

It starts the RMI registry used by Java Remote Method Invocation applications.

RMI is an older Java technology and is much less common in modern Spring Boot development.

---

# How these tools fit together

Think of the JDK like this:

```text
                    JDK
                     │
          ┌──────────┴──────────┐
          │                     │
       Development           Operations
          │                     │
     ┌────┴────┐          ┌─────┴─────┐
     │         │          │           │
   javac     jshell      jcmd       jconsole
     │         │          │           │
     ↓         ↓          ↓           ↓
  .class   experiments  diagnose    monitor
     │
     ↓
    jar
     │
     ↓
  application
     │
     ├── jps
     ├── jstack
     ├── jstat
     ├── jmap
     └── jcmd
```

And for distribution:

```text
Java source
    │
   javac
    ↓
.class
    │
   jar
    ↓
app.jar
    │
    ├── jdeps ──→ dependency analysis
    │
    ├── jlink ──→ custom Java runtime
    │
    └── jpackage ──→ native installer
```

---

# ⭐ Tools you should learn first

Since you're learning **Java + Spring Boot**, don't try to memorize every JDK command.

Learn them in this order:

### Beginner

```text
java
javac
jar
jshell
javadoc
javap
```

### JVM / production

```text
jps
jcmd
jstack
jstat
jmap
jconsole
```

### Packaging

```text
jdeps
jlink
jpackage
```

### Security

```text
keytool
jarsigner
```

### Advanced / older technologies

```text
jdb
jinfo
serialver
rmiregistry
```

A particularly useful workflow for your **Spring Boot** projects is:

```bash
# Start application
java -jar app.jar

# Find it
jps

# Inspect JVM
jcmd PID VM.flags

# Inspect threads
jcmd PID Thread.print

# Or
jstack PID

# Inspect GC
jstat -gc PID 1000

# Open GUI monitoring
jconsole
```

That gives you a practical foundation for **Java development + JVM troubleshooting**, rather than just memorizing the JDK commands.
