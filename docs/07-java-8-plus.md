# 07 - Functional Programming & Streams

For 5-10 years of experience, using Streams is basic. Understanding their lazy evaluation, pipeline optimizations, and edge cases is required.

---

## 🏗 Stream API Internals
- **Lazy Evaluation**: Intermediate operations (`filter`, `map`) do not execute until a terminal operation (`collect`, `count`) is invoked.
- **Pipeline Processing**: Elements are processed vertically down the pipeline. If `limit(1)` is met, the rest of the stream is not processed (Short-circuiting).
- **Spliterator**: The underlying engine for splitting a data source into chunks (for parallelization) and iterating.

## 🏗 Parallel Streams (Proceed with Caution)
Parallel streams use the global `ForkJoinPool.commonPool()`.
- **The Danger**: If you do blocking I/O (e.g., HTTP calls, DB queries) inside a `.parallelStream()`, you will exhaust the common pool, freezing other parallel streams across the *entire JVM*.
- **Rule of Thumb**: Only use parallel streams for CPU-bound tasks on very large datasets (`N > 10,000`).

## 🏗 Optional Best Practices & Anti-Patterns
`Optional<T>` was introduced to mitigate `NullPointerException`, but it is often misused.

**Anti-Patterns**:
- Using `.get()` without `.isPresent()`.
- Using `Optional` as a parameter to a method (forces callers to construct Optionals).
- Returning `null` from a method that returns an `Optional`.

**Best Practices**:
```java
// Good: Functional mapping and fallback
String name = userOptional.map(User::getName).orElse("Unknown");

// Good: Compute expensive fallback only if empty
String name = userOptional.map(User::getName).orElseGet(() -> db.findDefaultName());
```

### Interview Question
*Why does modifying a local variable inside a lambda throw a compilation error?*
Lambdas can only capture "effectively final" local variables. Since lambdas might execute asynchronously on another thread, modifying a thread-local stack variable from another thread is fundamentally unsafe in the JVM architecture.

---
[⬅ Back to Interview Roadmap](../README.md)
