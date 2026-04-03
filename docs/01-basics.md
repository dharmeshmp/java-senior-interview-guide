# 01 - Java Basics

This section covers the fundamental building blocks of Java programming.

---

## 🏗 Java Syntax
Every Java program starts with a `class`. The entry point of an application is the `main` method.

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```

## 📦 Variables & Data Types
Java is a statically-typed language, meaning all variables must be declared before use.

### 🧩 Primitive Data Types
| Data Type | Size (bits) | Default Value | Example Value |
| :--- | :--- | :--- | :--- |
| `byte` | 8 | 0 | `100` |
| `short` | 16 | 0 | `30000` |
| `int` | 32 | 0 | `2000000000` |
| `long` | 64 | 0L | `10000000000L` |
| `float` | 32 | 0.0f | `5.75f` |
| `double` | 64 | 0.0d | `19.99` |
| `boolean`| 1 | false | `true` |
| `char` | 16 | '\u0000' | `'A'` |

### 🖇 Reference Types
Reference types include **Strings**, **Arrays**, and **Objects**.
```java
String greeting = "Hello, Java!";
int[] numbers = {1, 2, 3, 4, 5};
```

## 🛤 Control Flow
### ⚖ If-Else Statement
```java
int score = 85;
if (score >= 90) {
    System.out.println("Grade A");
} else if (score >= 80) {
    System.out.println("Grade B");
} else {
    System.out.println("Grade C");
}
```

### 🔄 Switch Statement
```java
int day = 3;
switch (day) {
    case 1: System.out.println("Monday"); break;
    case 2: System.out.println("Tuesday"); break;
    case 3: System.out.println("Wednesday"); break;
    default: System.out.println("Another day");
}
```

## ♾ Loops
### 🔁 For Loop
```java
for (int i = 1; i <= 5; i++) {
    System.out.println("Iteration: " + i);
}
```

### 🔁 While Loop
```java
int count = 1;
while (count <= 5) {
    System.out.println("Count: " + count);
    count++;
}
```

## 🔢 Arrays
An array is a collection of similar types of data.
```java
// 1D Array
int[] arr = new int[5];
arr[0] = 10;

// 2D Array
int[][] matrix = {
    {1, 2, 3},
    {4, 5, 6}
};
```

---
[⬅ Back to Roadmap](../README.md)
