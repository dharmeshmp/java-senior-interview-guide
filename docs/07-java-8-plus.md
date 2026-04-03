# 07 - Functional Programming & Streams

For 5-10 years of experience, using Streams is basic. Understanding their lazy evaluation, pipeline optimizations, and edge cases is required.

---

## 🏗 Functional Programming in Java
Historically, Java was strictly an Imperative / Object-Oriented language (focusing on *how* to do things using mutable state and loops). 
With Java 8, Java embraced **Declarative / Functional** paradigms (focusing on *what* to do, treating functions as first-class citizens, and minimizing mutable state).

### Built-in Core Functional Interfaces (`java.util.function`)
Java provides four primary functional interfaces that form the backbone of the Stream API and lambda expressions:

1. **`Predicate<T>`**
   - **Signature**: `boolean test(T t);`
   - **Usage**: Used for evaluating a condition (e.g., filtering a list).
   - **Example**: `Predicate<String> isLong = s -> s.length() > 5;`

2. **`Function<T, R>`**
   - **Signature**: `R apply(T t);`
   - **Usage**: Transforms or maps an input to an output.
   - **Example**: `Function<String, Integer> stringLength = s -> s.length();`

3. **`Consumer<T>`**
   - **Signature**: `void accept(T t);`
   - **Usage**: Takes an argument and produces a side-effect (no return value).
   - **Example**: `Consumer<String> printer = s -> System.out.println(s);`

4. **`Supplier<T>`**
   - **Signature**: `T get();`
   - **Usage**: Takes no arguments but generates or supplies a value (great for lazy evaluation).
   - **Example**: `Supplier<Double> randomValue = () -> Math.random();`

*(Note: There are also `Bi` variants like `BiFunction<T, U, R>`, `BiPredicate<T, U>`, and primitive specializations like `IntFunction`, `DoubleSupplier` to avoid auto-boxing overhead).*


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

### Interview Questions
**Q1: What is a Functional Interface? Can we extend a Functional Interface?**
- A **Functional Interface** is an interface that contains exactly one abstract method (often annotated with `@FunctionalInterface`). It can contain multiple `default` or `static` methods. They are used as target types for Lambda expressions.
- **Can we extend it?** Yes. If Interface B extends Functional Interface A and does not declare any new abstract methods, B is also a functional interface. If B declares a *new* abstract method, it ceases to be a functional interface and cannot be used with lambdas.

**Q2: What is the Stream API?**
- The Stream API (`java.util.stream`) is an abstraction to process collections of objects in a functional-declarative manner. Instead of imperative loops (`for`, `while`), you define a pipeline of transformations (`map`, `filter`) and reductions (`reduce`, `collect`). Streams do not store data, they only process the underlying source.

**Q3: Why does modifying a local variable inside a lambda throw a compilation error?**
Lambdas can only capture "effectively final" local variables. Since lambdas might execute asynchronously on another thread, modifying a thread-local stack variable from another thread is fundamentally unsafe in the JVM architecture.


---
[⬅ Back to Interview Roadmap](../README.md)
