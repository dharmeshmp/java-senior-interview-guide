# 06 - Advanced Concurrency & Multi-threading

Concurrency is heavily tested in senior interviews. You must understand atomicity, visibility, and modern concurrent APIs.

---

## 🏗 Java Memory Model (JMM)
JMM dictates how threads interact through memory.
- **Visibility**: If Thread A writes a variable, Thread B might only see a cached CPU register version. Use `volatile` to force reads/writes directly to main memory.
- **Atomicity**: Increments (`i++`) are read-modify-write operations and are NOT atomic. Use `AtomicInteger` (which uses CAS - Compare And Swap instruction at the hardware level).
- **Happens-Before**: A guarantee that the results of one operation are visible to a subsequent operation (e.g., `unlock` on a monitor happens-before a `lock` on the same monitor).

## 🏗 Executors & CompletableFuture
Managing raw `Thread` objects is discouraged.
- **ExecutorService**: Thread pools (`FixedThreadPool`, `CachedThreadPool`).
- **CompletableFuture (Java 8)**: The foundation of modern async programming in Java. Allows chaining non-blocking callbacks.

```java
CompletableFuture.supplyAsync(() -> fetchUserData())
                 .thenApply(user -> processUser(user))
                 .thenAccept(result -> System.out.println(result))
                 .exceptionally(ex -> { log.error(ex); return null; });
```

## 🏗 ThreadLocal
Provides thread-local variables. Each thread has its own isolated instance.
- **Risk**: Highly prone to memory leaks in web servers (like Tomcat). If the thread goes back to the pool without `ThreadLocal.remove()`, the object is never garbage collected.

## 🏗 Virtual Threads (Project Loom - Java 21)
Heavyweight OS threads limit the "One-Thread-Per-Request" model.
- **Virtual Threads** are lightweight threads managed by the JVM, not the OS.
- Millions can run simultaneously. They "unmount" from the carrier OS thread during blocking I/O.
- Usage: `Executors.newVirtualThreadPerTaskExecutor()`.

### Interview Questions
**Q1: How do you create a thread in Java, and which method is better?**
In Java, you create a thread either by **extending the `Thread` class** or by **implementing the `Runnable` interface** (or passing a lambda).
- **Which is better?** Implementing `Runnable` is substantially better. 
  1. Java does not support multiple inheritance, so extending `Thread` severely limits your class design.
  2. Implementations of `Runnable` can be easily shared or passed to Thread Pools (`ExecutorService`), whereas `Thread` extensions couple the thread management directly with the task logic.
  3. Conceptually, a task (`Runnable`) is not a "kind of" thread; it's work *executed* by a thread.

**Q2: What is a Deadlock and how do you prevent it?**
Deadlock occurs when two or more threads hold locks and wait for locks held by others, forming a dependency cycle.
**Prevention**: Always acquire locks in a consistent global order across all threads. Use timeouts (`tryLock(time)` from `ReentrantLock`).

**Q3: What is the difference between `Future` and `CompletableFuture`?**
- **`Future` (Java 5)**: Represents the result of an asynchronous computation, but it is heavily limited. To get the result, you must call `.get()`, which **blocks** the calling thread until the computation is done. You also cannot chain multiple Futures together or handle exceptions elegantly.
- **`CompletableFuture` (Java 8)**: An extension of `Future` that supports completely **non-blocking** asynchronous, event-driven programming. You can attach callbacks (`thenApply`, `thenAccept`) to be executed automatically when the task completes. You can also easily combine multiple futures (`thenCombine`, `allOf`) and handle exceptions asynchronously (`exceptionally`, `handle`).


---
[⬅ Back to Interview Roadmap](../README.md)
