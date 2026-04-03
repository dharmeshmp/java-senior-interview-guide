# 04 - Exception Handling at Scale

For enterprise applications, exceptions must be handled efficiently, comprehensively, and gracefully to prevent data corruption and resource leaks.

---

## 🏗 The Performance Cost of Exceptions
Generating an exception is extremely slow in Java.
- **Why?**: The method `Throwable.fillInStackTrace()` must walk the execution stack to build the stack trace.
- **Optimization**: If utilizing exceptions for business logic (which is generally an anti-pattern), you can override `fillInStackTrace()` to return `this` for custom exceptions without stack trace gathering.

## 🏗 Try-With-Resources & Suppressed Exceptions
Introduced in Java 7, `try-with-resources` ensures `AutoCloseable` resources are closed automatically.
- **Suppressed Exceptions**: If an exception occurs in the `try` block, and closing the resource also throws an exception, the original exception is preserved, and the closing exception is added as a "Suppressed Exception".
  
```java
try (BufferedReader br = new BufferedReader(new FileReader(path))) {
    return br.readLine();
} catch (IOException e) {
    for (Throwable supp : e.getSuppressed()) {
        System.out.println(supp);
    }
}
```

## 🏗 Exception Handling in Distributed Systems (Spring)
Never leak stack traces or raw database exceptions to the client API response (security risk).
- Use `@ControllerAdvice` or `@RestControllerAdvice` in Spring to intercept all application exceptions globally.
- Standardize Error Responses (e.g., `ProblemDetail` or RFC 7807 specs).

## 🏗 Exceptions in Functional Paradigms (Java 8 Streams)
Lambdas do not play nicely with checked exceptions because functional interfaces like `Consumer` and `Function` do not declare `throws`.
- **Solution**: Wrap checked exceptions in `RuntimeException` within the lambda, or use a utility like "Sneaky Throws" (Lombok) or write a custom functional interface wrapper.

### Interview Question
*What is the difference between `Error` and `Exception`?*
Both inherit from `Throwable`. `Error` represents severe system-level problems (e.g., `OutOfMemoryError`, `StackOverflowError`) that the application typically should not catch. `Exception` represents conditions that a reasonable application might want to catch and recover from.

---
[⬅ Back to Interview Roadmap](../README.md)
