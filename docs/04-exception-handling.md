# 04 - Exception Handling

Exceptions are unforeseen events that occur during program execution, interrupting the normal flow.

---

## 🏗 Basics of Exception Handling
A `try-catch` block is used to handle exceptions.

```java
try {
    int num = 10 / 0; // ArithmeticException
} catch (ArithmeticException e) {
    System.out.println("Error: " + e.getMessage());
} finally {
    System.out.println("Finally block always executes.");
}
```

## 🏗 Multiple Catch Blocks
Catching different types of exceptions.
```java
try {
    int[] arr = {1, 2, 3};
    System.out.println(arr[5]); // ArrayIndexOutOfBoundsException
} catch (ArithmeticException e) {
    System.out.println("Arithmetic error");
} catch (ArrayIndexOutOfBoundsException e) {
    System.out.println("Array index error");
} catch (Exception e) {
    System.out.println("General exception");
}
```

## 🧩 Throw & Throws Keywords
- **throw**: Used to throw an exception explicitly.
- **throws**: Used in a method signature to declare exceptions it might throw.

```java
public void checkValidAge(int age) throws Exception {
    if (age < 18) {
        throw new Exception("Age not valid for voting!");
    } else {
        System.out.println("Age valid!");
    }
}
```

## 🛠 Custom Exceptions
You can create your own exception class by extending `Exception`.
```java
class InvalidUserException extends Exception {
    public InvalidUserException(String message) {
        super(message);
    }
}
```

---
[⬅ Back to Roadmap](../README.md)
