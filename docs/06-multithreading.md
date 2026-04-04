# 06 - Concurrency & Multithreading (Professional Grade)

Mastering Java Concurrency is the hallmark of a senior developer. You must understand not just how to start a thread, but how memory visibility, locking strategies, and modern reactive patterns work under the hood.

---

## 🏗 1. Java Memory Model (JMM) & Visibility
The JMM defines how threads interact through memory. 
- **The Problem**: Each CPU core has its own cache. Without proper synchronization, Thread A might update a variable in its cache, while Thread B reads an outdated value from its own cache or main memory.
- **`volatile`**: Ensures **Visibility**. Any write to a volatile variable is immediately flushed to main memory, and any read is pulled from main memory. 
  - **TRAP**: `volatile` does **NOT** ensure atomicity. `volatile count++` is still thread-unsafe because it involves a read-modify-write cycle.
- **`synchronized`**: Ensures both **Visibility** and **Atomicity**. It creates a "happens-before" relationship.

---

## 🏗 2. Synchronization & Locks
### Intrinsic Locks (`synchronized`)
- **Method Level**: Locks the `this` object (instance) or `Class` object (static).
- **Block Level**: More granular. You can lock on a specific "monitor" object to reduce contention.
- **Wait/Notify**: `wait()`, `notify()`, and `notifyAll()` must be called inside a `synchronized` block, or they throw `IllegalMonitorStateException`.

### Explicit Locks (`ReentrantLock`)
- **Advanced Features**: Fairness (prevents starvation), `tryLock()` (non-blocking), and multiple `Condition` objects.
- **`ReadWriteLock`**: Allows multiple concurrent readers but exclusive access for one writer. Perfect for read-heavy caches.

---

## 🏗 3. Thread Lifecycle & States
A thread can be in one of the following `Thread.State`:
1. **NEW**: Created but not yet started.
2. **RUNNABLE**: Executing in the JVM (may be waiting for OS resources).
3. **BLOCKED**: Waiting for an intrinsic lock.
4. **WAITING**: Waiting indefinitely for another thread (via `wait`, `join`, `park`).
5. **TIMED_WAITING**: Waiting for a specified time (`sleep`, `wait` with timeout).
6. **TERMINATED**: Execution finished.

**Trap**: `sleep()` keeps the lock; `wait()` releases the lock.

---

## 🏗 4. Advanced Executor Concepts
- **FixedThreadPool**: Uses a fixed number of threads and an unbounded queue.
- **CachedThreadPool**: Creates new threads as needed; good for many short-lived tasks.
- **ScheduledExecutorService**: For tasks that need to run periodically.
- **ForkJoinPool**: Uses the **Work-Stealing Algorithm**. Idle threads "steal" tasks from the tail of busy threads' queues. Perfect for recursive, CPU-bound tasks.

---

## 🏗 5. Concurrent Collections
- **`ConcurrentHashMap`**: Uses CAS (Compare-And-Swap) and synchronized bucket heads. No global locking.
- **`CopyOnWriteArrayList`**: Thread-safe without locking for reads. Every write creates a new copy of the underlying array.
- **Blocking Queues**: `ArrayBlockingQueue` (fixed) vs `LinkedBlockingQueue` (optional capacity). Used heavily in **Producer-Consumer** patterns.

---

## 🏗 6. CompletableFuture (Asynchronous Programming)
- **`thenCompose`**: Used for flattening nested Futures (like `flatMap`).
- **`thenCombine`**: Used to combine two independent futures when both finish.
- **`allOf` / `anyOf`**: For coordinating multiple asynchronous tasks.

---

## 🏗 7. Virtual Threads (Project Loom)
Introduced in Java 21, Virtual Threads are lightweight, user-mode threads.
- **Difference**: Platform threads are 1:1 with OS threads (expensive). Virtual threads are M:N (cheap, millions can exist).
- **Behavior**: When a virtual thread does blocking I/O, the JVM "unmounts" it from the carrier (platform) thread and mounts another one, maximizing throughput without blocking OS threads.

---

## 🏗 8. High-Value Interview Questions

**Q1: Difference between `synchronized` and `ReentrantLock`?**
`synchronized` is simpler and automatically releases the lock. `ReentrantLock` offers fairness, `tryLock()`, interruptible locks, and multi-condition support, but requires manual `unlock()` in a `finally` block.

**Q2: What is the "Double-Checked Locking" problem?**
Used in Singletons. If the instance variable is NOT `volatile`, a thread might see a partially initialized object because of instruction reordering.

**Q3: What happens if two threads call `HashMap.put()` simultaneously?**
In Java 7, it could cause an infinite loop during resizing. In Java 8+, it leads to data loss (one write overwrites another) or corrupts the internal tree/linked-list structure.

**Q4: What is CAS (Compare-And-Swap) and the ABA Problem?**
CAS is an atomic CPU instruction. The **ABA Problem** occurs when a value changes from A to B and back to A. A thread doing CAS sees 'A' and assumes nothing changed. **Fix**: Use `AtomicStampedReference`.

**Q5: What is Thread Starvation vs Livelock?**
- **Starvation**: A thread is permanently denied access to resources because others have higher priority.
- **Livelock**: Threads keep changing states in response to each other but make no progress (like two people trying to pass each other in a hallway).

**Q6: Why is `Thread.stop()` deprecated?**
It is inherently unsafe. It stops the thread instantly, releasing all held locks, which can leave objects in an inconsistent, corrupted state.

**Q7: Difference between `Future` and `CompletableFuture`?**
`Future` is blocking (`get()`). `CompletableFuture` is non-blocking, supports callbacks (`thenApply`), and allows complex chaining and exception handling.

---
[⬅ Back to Interview Roadmap](../README.md)
