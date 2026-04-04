# 06 - Advanced Multithreading & Concurrency

Concurrency is notoriously one of the hardest topics in Java. Senior-level interviews focus heavily on the Java Memory Model (JMM), fine-grained locking, modern asynchronous architectures, and edge-case race conditions.

---

## 🏗 1. Thread Lifecycle & States
A Java thread transitions through exactly these states (`Thread.State` enum):
1. **`NEW`**: Instantiated but `start()` has not been called.
2. **`RUNNABLE`**: Executing in the JVM (or waiting for OS resources to be scheduled).
3. **`BLOCKED`**: Waiting to acquire a monitor lock (e.g., trying to enter a `synchronized` block).
4. **`WAITING`**: Waiting indefinitely for another thread to perform an action (via `Object.wait()`, `Thread.join()`, or `LockSupport.park()`).
5. **`TIMED_WAITING`**: Waiting for a specified period (via `Thread.sleep()`, `wait(timeout)`).
6. **`TERMINATED`**: Thread execution completed or aborted.

**`sleep()` vs `wait()`**:
- `sleep(ms)` is a `Thread` method. It pauses execution but **holds onto any acquired locks**.
- `wait()` is an `Object` method. It pauses execution and **releases the lock**, allowing other threads to acquire it.

---

## 🏗 2. Synchronization & Locks
### `synchronized` (Intrinsic Locks)
Every object in Java has a built-in intrinsic "monitor" lock. Using `synchronized` guarantees that only one thread can execute the block/method at a time.
- *Method level*: Locks the `this` instance (or `ClassName.class` if static).
- *Block level*: Locks the specific object passed: `synchronized(lockObj) { ... }`.

### `ReentrantLock` & `ReadWriteLock`
`java.util.concurrent.locks` provides explicit locking mechanisms.
- **`ReentrantLock`**: Like `synchronized` but offers advanced features:
  - **Fairness**: `new ReentrantLock(true)` grants the lock to the longest-waiting thread (prevents *Starvation*). `synchronized` is always unfair.
  - **TryLock**: `lock.tryLock()` attempts to acquire a lock without blocking indefinitely.
- **`ReadWriteLock`**: Maintains a pair of locks. Multiple threads can hold the Read lock simultaneously as long as no thread holds the Write lock.

---

## 🏗 3. Inter-Thread Communication
Threads communicate state via `wait()`, `notify()`, and `notifyAll()`.
- **Why must they be inside `synchronized`?** A thread must exclusively own an object's monitor before it can wait on it or wake other threads waiting on it. Doing so without ownership throws `IllegalMonitorStateException`.

**Producer-Consumer pattern** is classically solved using these methods, though modern Java prefers using `java.util.concurrent.BlockingQueue` (like `ArrayBlockingQueue` or `LinkedBlockingQueue`) which handles the wait/notify locking internally.

---

## 🏗 4. Volatile & Memory Visibility
The JMM permits thread-local caching of variables for performance.
- The `volatile` keyword guarantees **visibility**. It forces threads to read the variable directly from Main Memory, bypassing L1/L2 caches.
- It prevents compiler **instruction reordering**.
- **TRAP**: `volatile` does **NOT** guarantee atomicity. `volatile count; count++` is a dangerous race condition because `count++` is an immediate Read-Modify-Write operation. You cannot replace atomic locks with `volatile`.

---

## 🏗 5. Thread Safety, CAS & Architecture
### Compare-And-Swap (CAS) & Atomic Variables
Classes like `AtomicInteger` use hardware-level CAS instead of locking to ensure atomic updates.
- **The ABA Problem**: Thread 1 reads value 'A'. Thread 2 changes it from 'A' to 'B', then back to 'A'. Thread 1 checks if the value is still 'A'—it is, so it proceeds. But the state actually mutated in between. Resolved using `AtomicStampedReference`.

### Double-Checked Locking Trap
Creating Singletons using "double-checked locking" is fundamentally broken unless the instance variable is explicitly marked `volatile`. Without `volatile`, the memory allocating the object can be reordered, causing Thread B to see a partially constructed object.

### Thread Confinement & Immutability
The safest multithreaded architecture is stateless/immutable.
- **Immutable Objects**: Once created, their state cannot change (e.g., `String`). No synchronization needed.
- **Thread Confinement**: Using `ThreadLocal<T>` creates an isolated copy of an object completely bound to the single executing thread.

### False Sharing
Occurs when multiple CPU cores aggressively update independent variables that happen to sit on the identical CPU cache line (usually 64 bytes). The cache line constantly invalidates, causing severe performance drops. Resolved using `@Contended` mapping to pad memory.

---

## 🏗 6. Advanced Executors & Thread Pools
- **FixedThreadPool**: Creates a bounded number of permanent threads. Best for predictable CPU loads.
- **CachedThreadPool**: Creates new threads dynamically as needed, killing them after 60s of inactivity. Dangerous for unpredictable loads (can trigger OutOfMemory).
- **ScheduledExecutorService**: Dedicated for scheduling periodic tasks.
- **ForkJoinPool**: The engine behind Parallel Streams. It splits large tasks into smaller fragments. Uses a **Work-Stealing Algorithm** — idle threads steal tasks from the end of busy threads' queues, constantly optimizing parallel execution.

**`submit()` vs `execute()`**:
- `execute(Runnable)`: Returns `void`. If an exception hits, the thread terminates and prints a stack trace.
- `submit(Runnable/Callable)`: Returns a `Future`. Exceptions are perfectly trapped and swallowed inside the Future. They erupt only when you call `.get()`.

---

## 🏗 7. Future & CompletableFuture API
`CompletableFuture` (Java 8) supports fully non-blocking architectures.
- `thenApply()`: Maps a result to a new result.
- `thenAccept()`: Consumes the result (side-effect, returns void).
- `thenCompose()`: Chains asynchronous tasks dependently (similar to `flatMap`. Task B requires output from Task A).
- `thenCombine()`: Runs two disparate async tasks independently, then merges both mathematical results.
- `allOf() / anyOf()`: Wait for all futures in an array to finish, or proceed immediately when the very first one finishes.

---

## 🏗 8. Virtual Threads (Project Loom / Java 21)
Traditional platform threads are 1:1 wrappers around heavy OS threads (consuming ~1MB RAM each). Virtual Threads are cheap, lightweight JVM-managed threads.
- Context switching a Virtual Thread when it hits a blocking IO operation (e.g., waiting for a DB) costs almost nothing, as the JVM unmounts the virtual thread and mounts another on the active platform carrier thread.
- **When NOT to use them**: Do not use Virtual Threads for heavy, continuous CPU-bound tasks (e.g., video encoding). They are strictly designed to eliminate blocking IO bottlenecks at massive scale.

---

## 💡 Tricky Interview Questions

**Q1: What happens if two threads call `HashMap.put()` simultaneously?**
A severe race condition natively, but worse in Java 7: simultaneous resizing could trigger a circular linked list (infinite loop) hanging the CPU at 100%. In Java 8, it results in data loss (silently overwriting values).

**Q2: Why is `Thread.stop()` deprecated?**
It brutally forces a thread to halt immediately, instantly releasing all locks the thread held. This routinely leads to data corruption because objects are left internally midway through their updates.

**Q3: What is False Sharing?**
When independent variables being updated by separate threads land on the same CPU cache line block (64 bytes), forcing hardware caches to invalidate repeatedly and bottlenecking concurrency down to sync-levels.

**Q4: Livelock vs Deadlock?**
- **Deadlock**: Threads freeze entirely, waiting for locks they will never receive.
- **Livelock**: Threads remain technically active, constantly reacting to each other's lock-avoidance actions, but functionally making zero progress (like two people trying to step around each other in a hallway infinitely).

**Q5: What is Thread Starvation?**
When a high priority thread or deeply aggressive process hoards CPU time, causing a lower priority thread to technically remain `RUNNABLE` but never receive actual processor clock cycles. Solved via fair locking strategies.

---
[⬅ Back to Interview Roadmap](../README.md)
