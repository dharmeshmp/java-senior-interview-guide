# 01 - JVM Internals & Memory Management

For a developer with 5-10 years of experience, understanding "how" Java works under the hood is critical. You must be prepared to discuss memory leaks, GC pauses, and performance tuning.

---

## 🏗 JVM Architecture Deep Dive
### ClassHolders and Delegation
- **Bootstrap Classloader**: Loads core Java API classes (`rt.jar`).
- **Extension Classloader**: Loads extension directories.
- **Application Classloader**: Loads your application's classpath.
- **Interview Question**: *How do you resolve `ClassNotFoundException` vs `NoClassDefFoundError`?*
  - **Answer**: `ClassNotFoundException` occurs at runtime when using `Class.forName()`, `loadClass()`, etc. `NoClassDefFoundError` occurs if a class was present during compile-time but missing during runtime (often a static initialization failure).

### Memory Regions
- **Heap**: Shared memory where objects live. Divided into Young Gen (Eden, S0, S1) and Old Gen.
- **Metaspace**: Removed PermGen in Java 8. Stores class metadata, static variables, and constants. Grows automatically, using native memory.
- **Stack**: Thread-specific. Stores local primitives and object references.
- **PC Register & Native Method Stack**: Tracks instruction addresses for native code.

## 🗑 Garbage Collection (GC) Internals
### Algorithms
1.  **G1GC (Garbage First)**: Default in Java 9+. Partitions heap into regions. Good balance of throughput and low pause times. Uses concurrent marking.
2.  **ZGC / Shenandoah**: Ultra-low latency GCs (often < 1ms pauses) by doing compaction concurrently. Good for huge heaps (terabytes).

### Tuning & Troubleshooting
- **Interview Question**: *A production app is experiencing high CPU and "Stop-The-World" pauses. How do you troubleshoot?*
  - **Answer**: 
    1. Check GC logs (e.g., `-Xlog:gc`). See if Full GCs are happening frequently.
    2. Take a Heap Dump (`jmap`) and analyze in Eclipse MAT to find memory leaks.
    3. Look for large object allocations (triggering Humongous Allocations in G1GC).
    4. Consider tuning parameters: `-Xms` and `-Xmx` (set them equal to avoid resizing overhead), `-XX:MaxGCPauseMillis`.

## ⚙ JIT Compilation & Escape Analysis
- **JIT**: Translates heavily used bytecode ("hot spots") into native machine code at runtime.
- **Escape Analysis**: Optimization where the JIT compiler analyzes if an object is only used within a method (doesn't "escape"). If so, it might allocate the object on the **Stack** instead of the Heap, drastically reducing GC pressure. Synchronization might also be removed (Lock Elision).

---
[⬅ Back to Interview Roadmap](../README.md)
