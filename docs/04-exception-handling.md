# 04 - Advanced Exception Handling & Resiliency

Exception handling separates robust, production-grade applications from amateur ones. At a senior level, you are expected to understand the performance impact of exceptions, architectural error-handling strategies, and tricky JVM execution flow rules.

---

## 🏗 1. The Exception Hierarchy
All errors and exceptions in Java map back to the `java.lang.Throwable` class.
- **`Error`**: Represents severe system-level problems (e.g., `OutOfMemoryError`, `StackOverflowError`). Applications **should not** try to catch these, as the JVM is typically in an unrecoverable state.
- **`Exception`**: Represents conditions that a reasonable application might want to catch and recover from.
  - **Unchecked Exceptions (`RuntimeException`)**: Represents programming bugs (e.g., `NullPointerException`, `IllegalArgumentException`). The compiler does not force you to handle these.
  - **Checked Exceptions`: Represents external circumstances out of your control (e.g., `IOException`, `SQLException`). The compiler forces you to either `catch` them or declare them via `throws`.

*Architectural Debate:* Modern Java frameworks (like Spring) heavily favor **Unchecked Exceptions**. Checked exceptions break encapsulation, clutter method signatures across all application layers, and deal poorly with Lambdas/Streams.

---

## 🏗 2. The Performance Cost of Exceptions
Throwing an exception in Java is exceptionally slow. 
- **The Culprit**: `Throwable.fillInStackTrace()`. Whenever an exception is instantiated, the JVM must pause, walk up the execution stack, and populate the stack trace.
- **Optimization Strategy**: If you *must* use exceptions to control business flow (e.g., `ValidationException` or `UserNotFoundException`—though returning an `Optional` is better design), you can significantly boost performance by overriding stack trace generation:

```java
public class FastBusinessException extends RuntimeException {
    public FastBusinessException(String message) {
        super(message);
    }

    @Override
    public synchronized Throwable fillInStackTrace() {
        // Return 'this' without walking the stack trace
        return this;
    }
}
```

---

## 🏗 3. Try-With-Resources & Suppressed Exceptions
Introduced in Java 7, `try-with-resources` ensures `AutoCloseable` resources (like database connections, streams) are closed automatically, eliminating manual `finally` block boilerplate.

```java
try (BufferedReader br = new BufferedReader(new FileReader("file.txt"))) {
    return br.readLine();
} catch (IOException e) {
    // Handling
}
```

### Suppressed Exceptions
What happens if the `try` block throws an exception, and the automatic `close()` method *also* throws an exception?
Before Java 7, the `close()` exception would swallow the original `try` exception. In `try-with-resources`, the `close()` exception is attached to the original exception as a **Suppressed Exception**.

```java
try {
    // ...
} catch (Exception e) {
    for (Throwable supp : e.getSuppressed()) {
        System.out.println("Also failed to close: " + supp);
    }
}
```

---

## 🏗 4. Exceptions in Functional Paradigms (Lambdas)
Lambdas do not play nicely with checked exceptions because functional interfaces like `Consumer` and `Function` do not declare `throws Exception`.

**Solutions:**
1. **Wrap in RuntimeException** inside the lambda body.
2. **Sneaky Throws**: A technique (popularized by Lombok) to trick the compiler into throwing a checked exception without declaring it via generics:

```java
public static <E extends Throwable> void sneakyThrow(Throwable e) throws E {
    throw (E) e;
}
// Usage in lambda:
list.forEach(i -> {
    if (i == 0) sneakyThrow(new IOException("sneaky"));
});
```

---

## 🏗 5. Global Exception Handling (Spring/Microservices)
Never leak stack traces or raw database constraints out to the API response; it is a critical security vulnerability.

- Use **`@RestControllerAdvice`** and **`@ExceptionHandler`** in Spring to intercept all application exceptions globally.
- Standardize Error Responses using **ProblemDetail** (RFC 7807 spec), returning standard JSON fields like `type`, `title`, `status`, and `detail`.

---

## 💡 Tricky Interview Scenarios

**Q1: What happens if you return a value inside the `try` block, but update it inside the `finally` block?**
The value being returned is mathematically evaluated and stored *before* the `finally` block executes. Modifying the primitive variable in the `finally` block does not alter the returned value.
```java
public static int test() {
    int x = 1;
    try { x = 2; return x; } finally { x = 3; }
}
// Outputs: 2
```

**Q2: What happens if both `try` and `finally` use the `return` keyword?**
The `finally` block's `return` statement **completely overrides** the `try` block's return. 
```java
public static int test() {
    try { return 1; } finally { return 2; }
}
// Outputs: 2
```

**Q3: If the `try` block throws an exception, but the `finally` block also throws an exception, which one propagates to the caller?**
The exception thrown in the `fully` block will swallow and hide the exception thrown in the `try` block completely. The original error is lost.

**Q4: Under what exact circumstances will a `finally` block NOT execute?**
1. The JVM is aggressively terminated via `System.exit()`.
2. The JVM crashes externally (e.g., killed via OS `SIGKILL` or memory failure).
3. The host machine loses power.
4. An infinite loop prevents the `try` block from finishing.

**Q5: Can you catch `OutOfMemoryError`? Should you?**
Yes, you *can* catch it because it extends `Throwable`. However, you **should not**. Once an OOM is thrown, JVM integrity is compromised. Even attempting to run the `catch` block might trigger another OOM as you require memory to allocate the exception variables.

---
[⬅ Back to Interview Roadmap](../README.md)
