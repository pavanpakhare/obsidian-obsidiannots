[[java]]

# Java Multithreading 

Java multithreading is the ability to execute **multiple tasks concurrently** within a single Java process. It is essential for backend development, Spring Boot, high-performance applications, servers, and concurrent data processing.

---

# 1. What is a Thread?

A **thread** is the smallest unit of execution inside a process.

For example, a Spring Boot application may have:

```text
Java Process
│
├── Main Thread
├── HTTP Request Thread
├── HTTP Request Thread
├── Database Task Thread
└── Scheduled Task Thread
```

Multiple threads can execute independently while sharing the same process memory.

### Process vs Thread

|Process|Thread|
|---|---|
|Independent program execution|Execution unit inside process|
|Has separate memory|Shares process memory|
|More expensive|Cheaper|
|Communication is harder|Communication is easier|
|Example: Chrome + IntelliJ|Multiple tasks inside IntelliJ|

---

# 2. Why Multithreading?

Suppose you need to:

```text
Download file
Send email
Query database
Process data
```

Sequential execution:

```text
Download ──► Email ──► DB ──► Processing
```

With multiple threads:

```text
Download ────────┐
Email ───────────┤
DB ──────────────┼──► Processing
                 │
```

This can improve **throughput and responsiveness**, especially for I/O-bound workloads.

However:

> Multithreading does not automatically make every program faster.

For CPU-bound work, too many threads can actually make the application slower.

---

# 3. Creating a Thread

There are several approaches.

## Method 1 — Extend `Thread`

```java
class MyThread extends Thread {

    @Override
    public void run() {
        System.out.println("Thread running");
    }
}

public class Main {
    public static void main(String[] args) {

        MyThread thread = new MyThread();

        thread.start();
    }
}
```

Important:

```java
thread.start();
```

not:

```java
thread.run();
```

### Why?

`start()` creates/schedules a new thread.

`run()` is simply a normal method call.

```java
thread.run();
```

means:

```text
main thread
   │
   └── run()
```

Whereas:

```java
thread.start()
```

results in execution by another thread.

---

# 4. Using `Runnable`

Usually preferable when you only need to define a task.

```java
class MyTask implements Runnable {

    @Override
    public void run() {
        System.out.println("Task running");
    }
}

public class Main {

    public static void main(String[] args) {

        Runnable task = new MyTask();

        Thread thread = new Thread(task);

        thread.start();
    }
}
```

Or lambda:

```java
Thread thread = new Thread(() -> {
    System.out.println("Hello");
});

thread.start();
```

---

# 5. `Callable`

`Runnable` cannot return a result.

`Callable` can.

```java
Callable<Integer> task = () -> {
    return 10 + 20;
};
```

Difference:

|Runnable|Callable|
|---|---|
|`run()`|`call()`|
|No return value|Returns value|
|Cannot directly throw checked exception|Can throw checked exception|
|Used for tasks|Used when result is needed|

---

# 6. Thread Lifecycle

A Java thread has these important states:

```text
NEW
 │
 │ start()
 ▼
RUNNABLE
 │
 ├──────────────┐
 │              │
 ▼              ▼
BLOCKED       WAITING
 │              │
 └──────┬───────┘
        ▼
    TIMED_WAITING
        │
        ▼
     RUNNABLE
        │
        ▼
   TERMINATED
```

Java's `Thread.State` contains:

```java
NEW
RUNNABLE
BLOCKED
WAITING
TIMED_WAITING
TERMINATED
```

---

# 7. `start()` vs `run()`

Very common interview question.

```java
Thread t = new Thread(() -> {
    System.out.println(Thread.currentThread().getName());
});

t.start();
```

Creates a new execution path.

But:

```java
t.run();
```

doesn't create a new thread.

It executes `run()` on the current thread.

---

# 8. Naming Threads

```java
Thread thread = new Thread(
        () -> System.out.println("Running")
);

thread.setName("Worker-1");

thread.start();
```

Get name:

```java
Thread.currentThread().getName();
```

Example:

```java
System.out.println(
    Thread.currentThread().getName()
);
```

Thread names are extremely useful for debugging.

---

# 9. `sleep()`

```java
Thread.sleep(1000);
```

Pauses the current thread for approximately one second.

Example:

```java
Thread.sleep(2000);

System.out.println("Finished");
```

Important:

> `sleep()` does NOT release locks held by the thread.

---

# 10. `join()`

`join()` makes one thread wait for another thread to finish.

```java
Thread worker = new Thread(() -> {
    System.out.println("Worker running");
});

worker.start();

worker.join();

System.out.println("Worker finished");
```

Conceptually:

```text
Main
 │
 ├── start Worker
 │
 │      Worker
 │       │
 │       └── work
 │
 ├── join()
 │      ↓
 │    WAIT
 │
 └── continue
```

---

# 11. `interrupt()`

Interrupt is a mechanism for requesting that a thread stop what it is doing or respond to cancellation.

```java
thread.interrupt();
```

If a thread is sleeping:

```java
try {
    Thread.sleep(10000);
} catch (InterruptedException e) {
    Thread.currentThread().interrupt();
}
```

Best practice:

```java
catch (InterruptedException e) {
    Thread.currentThread().interrupt();
}
```

Don't casually swallow interruption.

---

# 12. Daemon Threads

A daemon thread is a background thread.

```java
Thread thread = new Thread(() -> {
    while (true) {
        System.out.println("Background task");
    }
});

thread.setDaemon(true);
thread.start();
```

The JVM can terminate when **no non-daemon threads remain**, even if daemon threads are still running.

Examples of background-style tasks can include monitoring or cleanup activities.

---

# 13. Thread Priority

Java provides:

```java
Thread.MIN_PRIORITY    // 1
Thread.NORM_PRIORITY   // 5
Thread.MAX_PRIORITY   // 10
```

Example:

```java
thread.setPriority(Thread.MAX_PRIORITY);
```

But don't rely on priority for application correctness.

The actual scheduling behavior depends on the JVM and operating system.

---

# 14. Race Condition

One of the most important concepts.

Suppose:

```java
int counter = 0;
```

Two threads execute:

```java
counter++;
```

It looks like one operation, but conceptually it involves:

```text
READ counter
ADD 1
WRITE counter
```

Possible execution:

```text
Thread A       Thread B

READ 0
               READ 0
ADD 1
               ADD 1
WRITE 1
               WRITE 1
```

Expected:

```text
2
```

Actual:

```text
1
```

This is a **race condition**.

---

# 15. Critical Section

A critical section is code that accesses shared mutable state and must be coordinated.

```java
counter++;
```

can be a critical section when `counter` is shared.

---

# 16. `synchronized`

One solution is:

```java
class Counter {

    private int count = 0;

    synchronized void increment() {
        count++;
    }

    int getCount() {
        return count;
    }
}
```

Now only one thread at a time can execute the synchronized instance method for the same object.

---

# 17. Synchronized Block

Instead of synchronizing the whole method:

```java
synchronized void increment() {
    count++;
}
```

you can synchronize only the required section:

```java
void increment() {

    synchronized (this) {
        count++;
    }
}
```

This can reduce unnecessary locking.

---

# 18. Object Monitor

Every Java object can be associated with a monitor used for intrinsic synchronization.

When you write:

```java
synchronized (obj) {
    // critical section
}
```

the thread must acquire that object's monitor before entering.

Conceptually:

```text
Thread A
   │
   ├── acquire monitor
   │
   ├── critical section
   │
   └── release monitor

Thread B
   │
   └── waits for monitor
```

---

# 19. Static `synchronized`

Instance method:

```java
synchronized void test() {
}
```

locks on:

```java
this
```

Static synchronized method:

```java
static synchronized void test() {
}
```

locks on:

```java
ClassName.class
```

Example:

```java
class Test {

    synchronized void instanceMethod() {
    }

    static synchronized void staticMethod() {
    }
}
```

They use different locks.

---

# 20. `volatile`

`volatile` provides **visibility guarantees** for a variable.

Example:

```java
class Worker {

    private volatile boolean running = true;

    void stop() {
        running = false;
    }

    void work() {

        while (running) {
            // work
        }
    }
}
```

Without appropriate synchronization/visibility, one thread may not promptly observe another thread's update.

### Important

`volatile` does **not** make compound operations atomic.

This is not made safe merely by `volatile`:

```java
volatile int count;

count++;
```

Because:

```text
read
+
write
```

are still separate operations.

---

# 21. Atomic Classes

Java provides:

```java
AtomicInteger
AtomicLong
AtomicBoolean
AtomicReference
```

Example:

```java
AtomicInteger counter = new AtomicInteger();

counter.incrementAndGet();

System.out.println(counter.get());
```

Instead of:

```java
counter++;
```

you can use atomic operations.

---

# 22. Atomic vs Volatile vs Synchronized

|Feature|volatile|Atomic|synchronized|
|---|---|---|---|
|Visibility|Yes|Yes|Yes|
|Atomic operations|Limited|Yes|Yes|
|Mutual exclusion|No|No|Yes|
|Compound operations|No|Certain atomic methods|Yes|
|Locking|No|Usually lock-free algorithms|Yes|

---

# 23. Executor Framework

Creating threads manually is usually not ideal for server applications.

Instead of:

```java
new Thread(task).start();
```

use an executor.

```java
ExecutorService executor =
        Executors.newFixedThreadPool(4);

executor.submit(() -> {
    System.out.println("Task running");
});

executor.shutdown();
```

Architecture:

```text
Application
     │
     ▼
ExecutorService
     │
     ▼
Thread Pool
 ┌────┬────┬────┬────┐
 T1   T2   T3   T4
```

---

# 24. Why Thread Pools?

Creating a new thread for every task can be expensive.

Thread pools:

- reuse threads
    
- control concurrency
    
- reduce thread-creation overhead
    
- provide task queues
    
- improve resource management
    

---

# 25. `Executors`

Common factory methods include:

```java
Executors.newFixedThreadPool(n)
Executors.newSingleThreadExecutor()
Executors.newCachedThreadPool()
Executors.newScheduledThreadPool(n)
```

However, for production server applications, explicitly configuring a `ThreadPoolExecutor` is often preferable because you can control queueing and rejection behavior.

---

# 26. Fixed Thread Pool

```java
ExecutorService executor =
        Executors.newFixedThreadPool(3);
```

If there are 10 tasks:

```text
3 workers
   │
   ├── Task
   ├── Task
   ├── Task
   │
   └── remaining tasks → queue
```

---

# 27. Single Thread Executor

```java
ExecutorService executor =
        Executors.newSingleThreadExecutor();
```

Only one worker executes tasks.

Useful when tasks must be processed sequentially.

---

# 28. Scheduled Executor

```java
ScheduledExecutorService scheduler =
        Executors.newScheduledThreadPool(2);

scheduler.schedule(
    () -> System.out.println("Hello"),
    5,
    TimeUnit.SECONDS
);
```

You can also schedule repeatedly:

```java
scheduler.scheduleAtFixedRate(
    () -> System.out.println("Running"),
    0,
    10,
    TimeUnit.SECONDS
);
```

---

# 29. `Future`

A `Future` represents the result of an asynchronous computation.

```java
ExecutorService executor =
        Executors.newFixedThreadPool(2);

Future<Integer> future =
        executor.submit(() -> 10 + 20);

Integer result = future.get();

System.out.println(result);

executor.shutdown();
```

`get()` waits if necessary.

---

# 30. `Future` Cancellation

```java
future.cancel(true);
```

Check:

```java
future.isDone();
future.isCancelled();
```

---

# 31. `Callable`

Complete example:

```java
ExecutorService executor =
        Executors.newFixedThreadPool(2);

Callable<Integer> task = () -> {
    return 100;
};

Future<Integer> future =
        executor.submit(task);

System.out.println(future.get());

executor.shutdown();
```

---

# 32. `CompletableFuture`

Modern Java asynchronous programming often uses `CompletableFuture`.

```java
CompletableFuture
        .supplyAsync(() -> "Hello")
        .thenApply(String::toUpperCase)
        .thenAccept(System.out::println);
```

Flow:

```text
supplyAsync
     ↓
 "Hello"
     ↓
thenApply
     ↓
 "HELLO"
     ↓
thenAccept
```

---

# 33. Combining Async Tasks

```java
CompletableFuture<String> user =
        CompletableFuture.supplyAsync(() -> "Pavan");

CompletableFuture<String> role =
        CompletableFuture.supplyAsync(() -> "Developer");

CompletableFuture<String> result =
        user.thenCombine(
            role,
            (u, r) -> u + " - " + r
        );

System.out.println(result.join());
```

---

# 34. `thenApply` vs `thenAccept` vs `thenRun`

### `thenApply`

Transforms a result.

```java
future.thenApply(x -> x * 2);
```

Returns another `CompletableFuture`.

### `thenAccept`

Consumes result.

```java
future.thenAccept(System.out::println);
```

### `thenRun`

Runs something after completion but doesn't receive the result.

```java
future.thenRun(() -> System.out.println("Done"));
```

---

# 35. Parallel Tasks

```java
CompletableFuture<String> a =
        CompletableFuture.supplyAsync(() -> "A");

CompletableFuture<String> b =
        CompletableFuture.supplyAsync(() -> "B");

CompletableFuture.allOf(a, b).join();

System.out.println(a.join());
System.out.println(b.join());
```

---

# 36. Locks

Java provides explicit locks through:

```java
java.util.concurrent.locks
```

Most commonly:

```java
ReentrantLock
```

Example:

```java
class Counter {

    private int count;

    private final ReentrantLock lock =
            new ReentrantLock();

    void increment() {

        lock.lock();

        try {
            count++;
        } finally {
            lock.unlock();
        }
    }
}
```

Always unlock in `finally`.

---

# 37. `synchronized` vs `ReentrantLock`

|synchronized|ReentrantLock|
|---|---|
|Simpler|More flexible|
|Automatic unlock|Manual unlock|
|No `tryLock()`|Supports `tryLock()`|
|No configurable fairness|Can configure fairness|
|JVM intrinsic monitor|Explicit lock|

Example:

```java
if (lock.tryLock()) {
    try {
        // work
    } finally {
        lock.unlock();
    }
}
```

---

# 38. ReadWriteLock

Useful when:

```text
Many reads
Few writes
```

Example:

```java
ReadWriteLock lock =
        new ReentrantReadWriteLock();

lock.readLock().lock();

try {
    // read
} finally {
    lock.readLock().unlock();
}
```

Write:

```java
lock.writeLock().lock();

try {
    // modify
} finally {
    lock.writeLock().unlock();
}
```

---

# 39. Semaphore

A semaphore limits the number of threads accessing a resource.

```java
Semaphore semaphore =
        new Semaphore(3);
```

Only three permits are initially available.

```java
semaphore.acquire();

try {
    // resource
} finally {
    semaphore.release();
}
```

Useful for limiting:

```text
Database connections
API requests
Expensive resources
```

---

# 40. CountDownLatch

Used when one or more threads need to wait until several operations finish.

```java
CountDownLatch latch =
        new CountDownLatch(3);
```

Workers:

```java
latch.countDown();
```

Waiting thread:

```java
latch.await();
```

Concept:

```text
Worker A ── countDown()
Worker B ── countDown()
Worker C ── countDown()
                  │
                  ▼
              count = 0
                  │
                  ▼
              Main continues
```

A `CountDownLatch` is generally one-shot; once its count reaches zero, it cannot be reset.

---

# 41. CyclicBarrier

Allows multiple threads to wait for each other at a synchronization point.

```java
CyclicBarrier barrier =
        new CyclicBarrier(3);
```

Each thread:

```java
barrier.await();
```

Concept:

```text
Thread A ──┐
Thread B ──┼── Barrier ──► continue
Thread C ──┘
```

Unlike `CountDownLatch`, a `CyclicBarrier` can be reused.

---

# 42. Phaser

`Phaser` is a more flexible synchronization mechanism for applications with multiple phases and dynamically changing parties.

```java
Phaser phaser = new Phaser(3);

phaser.arriveAndAwaitAdvance();
```

Think:

```text
Phase 1
   ↓
Phase 2
   ↓
Phase 3
```

---

# 43. BlockingQueue

Very important for producer-consumer problems.

```java
BlockingQueue<Integer> queue =
        new ArrayBlockingQueue<>(10);
```

Producer:

```java
queue.put(100);
```

Consumer:

```java
Integer value = queue.take();
```

Architecture:

```text
Producer
   │
   ▼
BlockingQueue
   │
   ▼
Consumer
```

The queue handles waiting when appropriate.

---

# 44. Producer-Consumer

```java
BlockingQueue<Integer> queue =
        new ArrayBlockingQueue<>(10);

Thread producer = new Thread(() -> {

    try {
        for (int i = 0; i < 100; i++) {
            queue.put(i);
        }
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    }
});

Thread consumer = new Thread(() -> {

    try {
        while (true) {
            Integer value = queue.take();
            System.out.println(value);
        }
    } catch (InterruptedException e) {
        Thread.currentThread().interrupt();
    }
});
```

This is much safer than manually implementing wait/notify in many cases.

---

# 45. `wait()`, `notify()`, `notifyAll()`

Legacy but important for interviews.

```java
synchronized (lock) {
    lock.wait();
}
```

Another thread:

```java
synchronized (lock) {
    lock.notify();
}
```

Or:

```java
lock.notifyAll();
```

### Important

`wait()`:

- releases the monitor
    
- puts the thread into waiting
    
- must be called while owning that object's monitor
    

Unlike:

```java
Thread.sleep()
```

which does **not** release the monitor.

---

# 46. `wait()` vs `sleep()`

|`wait()`|`sleep()`|
|---|---|
|Object method|Thread static method|
|Releases monitor|Does not release monitor|
|Used for coordination|Used for delay|
|Must be called while holding object's monitor|No monitor requirement|

---

# 47. Deadlock

Deadlock occurs when threads wait forever for locks held by each other.

Example:

```text
Thread A
  locks A
  waits for B

Thread B
  locks B
  waits for A
```

Result:

```text
A → waiting for B
B → waiting for A
```

Nobody can continue.

---

# 48. How to Prevent Deadlock?

### 1. Consistent lock ordering

Always:

```text
Lock A
   ↓
Lock B
```

Never sometimes:

```text
A → B
```

and elsewhere:

```text
B → A
```

### 2. Use `tryLock()`

```java
if (lock.tryLock(1, TimeUnit.SECONDS)) {
    try {
        // work
    } finally {
        lock.unlock();
    }
}
```

### 3. Reduce lock scope

Don't hold locks longer than necessary.

---

# 49. Starvation

Starvation occurs when a thread cannot get sufficient CPU time or access to a required resource because other threads continually dominate it.

Example:

```text
Thread A ──────── resource
Thread B ──────── waiting
Thread C ──────── waiting
Thread B never gets chance
```

---

# 50. Livelock

Threads are active but cannot make progress.

Example concept:

```text
Thread A → changes action
Thread B → changes action
Thread A → changes action
Thread B → changes action
```

They're not blocked, but useful work never completes.

---

# 51. Thread Safety

A class is thread-safe when it behaves correctly when accessed concurrently according to its contract.

Example unsafe code:

```java
class Counter {

    int count;

    void increment() {
        count++;
    }
}
```

Thread-safe version:

```java
class Counter {

    private final AtomicInteger count =
            new AtomicInteger();

    void increment() {
        count.incrementAndGet();
    }
}
```

---

# 52. Immutable Objects

Immutable objects are naturally easier to use safely across threads because their state cannot change after construction.

Example:

```java
public final class User {

    private final String name;

    public User(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }
}
```

---

# 53. Concurrent Collections

Normal collections:

```java
ArrayList
HashMap
HashSet
```

are generally not designed for concurrent mutation.

Java provides:

```java
ConcurrentHashMap
CopyOnWriteArrayList
BlockingQueue
ConcurrentLinkedQueue
ConcurrentLinkedDeque
```

---

# 54. `ConcurrentHashMap`

```java
ConcurrentHashMap<String, Integer> map =
        new ConcurrentHashMap<>();

map.put("Java", 10);

map.compute(
    "Java",
    (key, value) -> value + 1
);
```

It supports concurrent access more effectively than synchronizing an entire `HashMap`.

---

# 55. `CopyOnWriteArrayList`

Good when:

```text
Many reads
Very few writes
```

Example:

```java
CopyOnWriteArrayList<String> list =
        new CopyOnWriteArrayList<>();
```

Writes are more expensive because the underlying array is copied.

---

# 56. ThreadLocal

`ThreadLocal` provides a separate value for each thread.

```java
ThreadLocal<Integer> local =
        ThreadLocal.withInitial(() -> 0);

local.set(100);

System.out.println(local.get());
```

Concept:

```text
Thread A → value = 100
Thread B → value = 200
Thread C → value = 300
```

Each thread sees its own value.

### Important in thread pools

Because worker threads are reused, values in `ThreadLocal` can survive across tasks.

Therefore:

```java
try {
    local.set(value);
    // task
} finally {
    local.remove();
}
```

is often important when using thread pools.

---

# 57. Java Memory Model

This is an important interview topic.

The Java Memory Model, or JMM, defines rules for:

- visibility
    
- ordering
    
- atomicity
    
- synchronization
    

Threads may have their own working views of memory, while shared state ultimately resides in shared memory.

Conceptually:

```text
Thread A
  │
  ├── local/cache
  │
  ▼
Shared Memory
  ▲
  │
  ├── local/cache
  │
Thread B
```

Synchronization mechanisms establish required visibility and ordering guarantees.

---

# 58. Happens-Before

The **happens-before** relationship is fundamental to the JMM.

For example, an unlock happens-before a subsequent lock on the same monitor.

Also:

```java
Thread.start()
```

has happens-before effects for actions in the started thread.

And:

```java
thread.join()
```

provides visibility guarantees for actions performed by the terminated thread before the join returns.

`volatile` writes and subsequent reads of the same variable also establish a happens-before relationship.

---

# 59. Atomicity, Visibility, Ordering

Three major concurrency concepts:

### Atomicity

Operation appears indivisible.

### Visibility

One thread can observe another thread's changes.

### Ordering

Operations are observed in an allowed order.

Synchronization mechanisms address these concerns according to their semantics.

---

# 60. Fork/Join Framework

Designed for divide-and-conquer parallelism.

```java
ForkJoinPool pool =
        new ForkJoinPool();

pool.submit(() -> {
    // parallel work
});
```

Tasks can recursively split:

```text
Large Task
   │
   ├── Task A
   │   ├── A1
   │   └── A2
   │
   └── Task B
       ├── B1
       └── B2
```

---

# 61. Work Stealing

Fork/Join uses work-stealing techniques.

Conceptually:

```text
Worker A queue:
[A][B][C]

Worker B queue:
[D]

Worker B finishes D
     ↓
steals work
     ↓
[C]
```

This helps keep worker threads busy.

---

# 62. Parallel Streams

Java streams can run in parallel:

```java
list.parallelStream()
    .map(...)
    .filter(...)
    .toList();
```

But don't automatically use them.

Parallelism can hurt performance for:

- small collections
    
- cheap operations
    
- heavily synchronized operations
    
- I/O operations
    
- operations with ordering constraints
    

Measure before using them.

---

# 63. Virtual Threads

Modern Java provides **virtual threads**.

Example:

```java
Thread.startVirtualThread(() -> {
    System.out.println("Hello");
});
```

Or:

```java
Thread.ofVirtual()
      .start(() -> {
          System.out.println("Running");
      });
```

Virtual threads are lightweight JVM-managed threads designed especially for workloads with lots of blocking operations.

Conceptually:

```text
Traditional platform threads

Task → OS Thread
Task → OS Thread
Task → OS Thread


Virtual threads

Task ─┐
Task ─┤
Task ─┤
Task ─┤
Task ─┤
      ↓
 JVM schedules onto fewer carrier threads
```

---

# 64. Virtual Threads vs Platform Threads

|Platform Thread|Virtual Thread|
|---|---|
|Heavier|Much lighter|
|OS-backed|JVM-managed|
|Limited practical count|Can support very large numbers|
|Good for CPU work|Excellent for high-concurrency blocking workloads|
|More expensive to create|Cheap to create|

Virtual threads don't magically make CPU-bound work faster.

---

# 65. Virtual Thread Executor

```java
try (ExecutorService executor =
         Executors.newVirtualThreadPerTaskExecutor()) {

    Future<String> future =
        executor.submit(() -> {
            Thread.sleep(1000);
            return "Done";
        });

    System.out.println(future.get());
}
```

This is particularly useful for applications handling many concurrent blocking operations.

---

# 66. Platform Thread Pools vs Virtual Threads

Traditional:

```java
Executors.newFixedThreadPool(100);
```

Virtual:

```java
Executors.newVirtualThreadPerTaskExecutor();
```

Don't blindly replace every executor with virtual threads. You still need to consider external resource limits such as:

```text
Database connections
API rate limits
CPU
Memory
File descriptors
```

---

# 67. Spring Boot and Multithreading

In Spring Boot, you will commonly encounter thread pools around:

```text
HTTP requests
@Async
Schedulers
Messaging
Database connection pools
Virtual threads
```

For example:

```java
@Async
public void sendEmail() {
    // background task
}
```

You generally enable it with:

```java
@EnableAsync
```

and configure an appropriate executor.

---

# 68. Important Spring Concept

Don't assume:

```java
@Async
```

works for every call.

A common problem is self-invocation:

```java
this.sendEmail();
```

Because Spring's proxy mechanism may be bypassed.

Usually the asynchronous method should be invoked through the Spring-managed proxy.

---

# 69. Thread Pool Sizing

A simplified starting point:

### CPU-bound

Often around:

```text
number of CPU cores
```

or a small multiple depending on workload.

### I/O-bound

Can often use more concurrency because threads spend time waiting.

But don't blindly use:

```text
CPU cores × 100
```

Measure the workload and consider downstream limits.

---

# 70. Common Multithreading Architecture

A backend application may look like:

```text
                 Client
                   │
                   ▼
              Web Server
                   │
             Request Pool
                   │
          ┌────────┴─────────┐
          ▼                  ▼
       Service             Service
          │                  │
          ▼                  ▼
     DB Connection      External API
         Pool                │
                             ▼
                         Thread Pool
```

Notice that **thread pools and connection pools are different things**.

---

# 71. Thread Pool vs Connection Pool

Thread pool:

```text
Controls executing threads
```

Connection pool:

```text
Controls database connections
```

Example:

```text
100 request threads
       │
       ▼
10 DB connections
```

100 requests may exist concurrently, but only a limited number may execute database operations simultaneously.

---

# 72. Common Interview Questions

## Beginner

### Q1. What is a thread?

A thread is an independent path of execution within a process.

### Q2. How do you create a thread?

Common approaches include:

```java
Thread
Runnable
Callable + ExecutorService
```

and modern Java also supports virtual threads.

### Q3. Difference between `start()` and `run()`?

`start()` initiates a new thread of execution; `run()` is an ordinary method call when invoked directly.

### Q4. What is `sleep()`?

It pauses the current thread for a specified duration and does not release intrinsic monitors.

### Q5. What is `join()`?

It allows one thread to wait for another thread to terminate.

---

# 73. Intermediate Interview Questions

### Q6. What is race condition?

When concurrent execution causes the result to depend on timing/interleaving of threads accessing shared state.

### Q7. What is synchronization?

A mechanism for controlling concurrent access and establishing memory-visibility/ordering guarantees.

### Q8. What is `volatile`?

A field modifier that provides specific visibility and ordering guarantees for accesses to that field. It does not provide general mutual exclusion or make compound operations atomic.

### Q9. Is `volatile int i; i++` thread-safe?

No.

```java
i++;
```

is a read-modify-write operation.

### Q10. What is deadlock?

A situation where threads are permanently waiting for resources held by each other.

---

# 74. Advanced Interview Questions

### Q11. `synchronized` vs `Lock`?

`synchronized` is simpler and automatically manages monitor release.

`Lock` provides additional capabilities such as:

```java
tryLock()
lockInterruptibly()
fairness
multiple conditions
```

### Q12. `wait()` vs `sleep()`?

`wait()` releases the object's monitor and is used for coordination.

`sleep()` pauses the thread and does not release monitors.

### Q13. `notify()` vs `notifyAll()`?

`notify()` wakes one waiting thread.

`notifyAll()` wakes all threads waiting on that monitor.

The awakened threads still have to reacquire the monitor before proceeding.

### Q14. What is thread starvation?

A thread repeatedly fails to obtain sufficient CPU time or required resources.

### Q15. What is livelock?

Threads remain active and repeatedly react to each other but make no useful progress.

---

# 75. Very Important Interview Question

### Is `HashMap` thread-safe?

No.

Use:

```java
ConcurrentHashMap
```

for suitable concurrent-map use cases.

Or synchronize externally when appropriate.

---

# 76. `ArrayList` vs `CopyOnWriteArrayList`

```text
ArrayList
   ↓
not designed for concurrent modification

CopyOnWriteArrayList
   ↓
safe concurrent access
   ↓
excellent when reads >> writes
```

---

# 77. `HashMap` vs `ConcurrentHashMap`

|HashMap|ConcurrentHashMap|
|---|---|
|Not thread-safe|Designed for concurrent access|
|Allows one null key|Does not permit null keys/values|
|External synchronization needed for shared mutation|Provides concurrent operations|
|General purpose|Concurrent workloads|

---

# 78. What is ExecutorService?

It manages asynchronous task execution using a pool or other execution strategy.

Example:

```java
ExecutorService executor =
        Executors.newFixedThreadPool(4);

executor.submit(task);

executor.shutdown();
```

---

# 79. `execute()` vs `submit()`

```java
executor.execute(task);
```

is used for a `Runnable` without a returned `Future`.

```java
executor.submit(task);
```

returns a `Future`.

Example:

```java
Future<?> future =
        executor.submit(task);
```

---

# 80. What happens if ExecutorService isn't shut down?

Its worker threads may keep the application alive and resources may not be released as intended.

Therefore:

```java
executor.shutdown();
```

or appropriate lifecycle management is important.

---

# 81. `shutdown()` vs `shutdownNow()`

### `shutdown()`

Stops accepting new tasks and allows already submitted tasks to finish.

### `shutdownNow()`

Attempts to stop currently executing tasks, typically by interrupting their threads, and returns tasks that never started.

It does not forcibly kill threads.

---

# 82. What is a thread pool?

A collection of reusable worker threads that execute submitted tasks.

```text
Tasks
 ↓
Queue
 ↓
Thread Pool
 ├── T1
 ├── T2
 ├── T3
 └── T4
```

---

# 83. What is `ThreadPoolExecutor`?

The main configurable implementation behind many executor use cases.

Conceptually:

```java
ThreadPoolExecutor(
    corePoolSize,
    maximumPoolSize,
    keepAliveTime,
    unit,
    workQueue,
    threadFactory,
    handler
)
```

Important parameters:

```text
corePoolSize
maximumPoolSize
workQueue
keepAliveTime
ThreadFactory
RejectedExecutionHandler
```

---

# 84. RejectedExecutionHandler

What happens when an executor cannot accept another task?

Common policies include:

```java
AbortPolicy
CallerRunsPolicy
DiscardPolicy
DiscardOldestPolicy
```

Example:

```java
new ThreadPoolExecutor.CallerRunsPolicy()
```

The submitting thread may execute the task itself when the pool is saturated.

---

# 85. Common Multithreading Mistakes

### Mistake 1

```java
new Thread(task).start();
```

for every request.

Better:

```text
ExecutorService
```

or an appropriate framework-managed executor.

### Mistake 2

Using:

```java
volatile
```

as a replacement for all synchronization.

### Mistake 3

Holding locks for a long time.

### Mistake 4

Using too many threads.

### Mistake 5

Ignoring `InterruptedException`.

### Mistake 6

Using shared mutable state unnecessarily.

### Mistake 7

Using parallel streams without measuring.

### Mistake 8

Creating deadlocks with inconsistent lock ordering.

---

# 86. Practical Interview Coding Problem

## Problem

Create a counter accessed by 10 threads, where each thread increments it 1,000 times.

Unsafe:

```java
class Counter {

    int count = 0;

    void increment() {
        count++;
    }
}
```

Correct using `AtomicInteger`:

```java
class Counter {

    private final AtomicInteger count =
            new AtomicInteger();

    void increment() {
        count.incrementAndGet();
    }

    int getCount() {
        return count.get();
    }
}
```

Then:

```java
ExecutorService executor =
        Executors.newFixedThreadPool(10);

Counter counter = new Counter();

for (int i = 0; i < 10; i++) {

    executor.submit(() -> {

        for (int j = 0; j < 1000; j++) {
            counter.increment();
        }
    });
}

executor.shutdown();

executor.awaitTermination(
        1,
        TimeUnit.MINUTES
);

System.out.println(counter.getCount());
```

Expected:

```text
10000
```

---

# 87. Interview Mental Model

For Java multithreading, remember this hierarchy:

```text
                    MULTITHREADING
                         │
        ┌────────────────┼─────────────────┐
        │                │                 │
      Threads         Shared State       Async Tasks
        │                │                 │
   ┌────┴────┐       ┌───┴────┐       ┌────┴────┐
 Thread   Runnable   Lock   Atomic   Future  CompletableFuture
                    │
             ┌──────┼──────────┐
             │      │          │
        synchronized Lock   ReadWriteLock
             │
       wait/notify
```

Then:

```text
Concurrency utilities
        │
 ┌──────┼───────────────┐
 │      │               │
Queue  Synchronizers   Collections
 │      │               │
BQ    Latch/Barrier   ConcurrentHashMap
 │    Semaphore        CopyOnWriteArrayList
 │    Phaser
```

And modern Java:

```text
Virtual Threads
      │
      ▼
High-concurrency blocking workloads
```

---

# 88. Best Learning Order

For Java/Spring backend interviews, learn in this order:

```text
1. Process vs Thread
       ↓
2. Thread lifecycle
       ↓
3. Thread / Runnable / Callable
       ↓
4. start / run / sleep / join / interrupt
       ↓
5. Race conditions
       ↓
6. synchronized
       ↓
7. volatile
       ↓
8. Atomic classes
       ↓
9. ExecutorService
       ↓
10. ThreadPoolExecutor
       ↓
11. Future / Callable
       ↓
12. CompletableFuture
       ↓
13. Lock / ReentrantLock
       ↓
14. wait / notify / notifyAll
       ↓
15. Concurrent collections
       ↓
16. BlockingQueue
       ↓
17. CountDownLatch / CyclicBarrier / Semaphore
       ↓
18. Deadlock / starvation / livelock
       ↓
19. Java Memory Model
       ↓
20. Fork/Join
       ↓
21. Parallel Streams
       ↓
22. Virtual Threads
       ↓
23. Spring @Async / task executors
```

**For interviews, the highest-priority topics are:** `start()` vs `run()`, thread lifecycle, race conditions, `synchronized`, `volatile`, atomic classes, `wait()`/`notify()`, `sleep()` vs `wait()`, `ExecutorService`, `Callable`/`Future`, `CompletableFuture`, locks, deadlock, concurrent collections, thread pools, and virtual threads.