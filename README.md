# Advanced Java Interview Guide (5-10 Years Experience)

Welcome to the Senior Java Developer Interview Guide. This repository is specifically tailored for experienced Java engineers preparing for system design and advanced technical interviews. It focuses on internal workings, architectural decisions, JVM internals, and concurrency.

## 🚀 Mastering the Java Ecosystem

### [1. JVM Internals & Memory Management](./docs/01-basics.md)
*   JVM Architecture (Classloaders, Runtime Data Areas, Execution Engine)
*   Garbage Collection (G1GC, ZGC, Shenandoah) & Tuning
*   JIT Compilation & Escape Analysis
*   Java Memory Model (JMM) & Happens-Before

### [2. Advanced OOP & Design Architecture](./docs/02-oops.md)
*   SOLID Principles in Practice
*   GoF Design Patterns (Factory, Strategy, Observer, Singleton issues)
*   Immutability (String Pool internals, Custom Immutable Classes)
*   Composition vs Inheritance (Liskov Substitution issues)

### [3. Collections API Deep Dive](./docs/03-collections.md)
*   Internal working of HashMap (Treeify threshold, bit masking)
*   ConcurrentHashMap (Node locking vs Segment locking in Java 8)
*   CopyOnWriteArrayList & Fail-Safe vs Fail-Fast Iterators
*   Choosing the right collection for High-Throughput systems

### [4. Exception Handling at Scale](./docs/04-exception-handling.md)
*   Performance cost of Exception generation (fillInStackTrace)
*   Global Exception Handling in Distributed Systems (ControllerAdvice)
*   Handling Exceptions in Java 8 Streams & CompletableFuture
*   Try-with-Resources and Suppressed Exceptions

### [5. NIO & High-Performance I/O](./docs/05-file-io.md)
*   Java NIO vs Standard I/O (Blocking vs Non-Blocking)
*   Channels, Buffers (ByteBuffer internals), and Selectors
*   Memory-Mapped Files (MappedByteBuffer) for low latency
*   Zero-Copy networking

### [6. Advanced Concurrency & Multi-threading](./docs/06-multithreading.md)
*   ThreadLocal internals and memory leaks
*   Volatile vs Atomic variables vs Synchronized
*   CompletableFuture, Fork/Join Pool, and ExecutorService tuning
*   Deadlocks, Livelocks, and Thread Starvation mitigation
*   Virtual Threads (Project Loom - Java 21)

### [7. Functional Programming & Streams (Java 8+)](./docs/07-java-8-plus.md)
*   Stream API Internals (Spliterator, Lazy Evaluation)
*   Parallel Streams (ForkJoinPool.commonPool issues)
*   Optional best practices & Anti-patterns
*   Method References & Functional Interfaces

### [8. Database Interactions & Transactions](./docs/08-jdbc.md)
*   Connection Pooling Internals (HikariCP optimizations)
*   Transaction Isolation Levels & Dirty, Phantom, Non-repeatable reads
*   CAP Theorem & ACID Properties in Distributed Systems
*   Spring Transaction Propagation (`@Transactional` proxy behavior)

### [9. Enterprise Frameworks (Spring & Hibernate)](./docs/09-frameworks-and-tools.md)
*   Spring Bean Lifecycle & Scopes
*   AOP Internals (JDK Dynamic Proxies vs CGLIB)
*   Spring Boot Auto-configuration magic
*   Hibernate N+1 Problem & Fetch Strategies
*   1st & 2nd Level Cache (Ehcache, Redis)

### [10. Web Development & API Design](./docs/10-web-dev.md)
*   Monolith vs Microservices Trade-offs
*   Synchronous vs Asynchronous Communication
*   REST vs gRPC vs GraphQL
*   Idempotency & Rate Limiting algorithms
*   Security (JWT, OAuth2)

### [11. Microservices Architecture & Design Patterns](./docs/11-microservices.md)
*   Strangler Fig & Sidecar Patterns
*   Saga, Outbox, and Event Sourcing (Distributed Transactions)
*   CQRS (Command Query Responsibility Segregation)
*   Circuit Breakers, Bulkheads, and Resiliency
*   Observability (Distributed Tracing, ELK)

---

## 💡 System Design Questions
Typically asked at the 5+ years experience level:
- *Design a URL Shortener.*
- *Design a high-throughput distributed Cache.*
- *Design an E-commerce inventory system preventing overselling.*
