# 07 - Java 8+ Features

Java 8 introduced some revolutionary features that transformed Java from a traditional Imperative language to a more Functional one.

---

## 🏗 Lambda Expressions
Lambda expressions allow us to treat functionality as a method argument, or code as data.
```java
// Traditional way
List<String> fruits = Arrays.asList("Apple", "Banana", "Cherry");
fruits.forEach(new Consumer<String>() {
    public void accept(String fruit) {
        System.out.println(fruit);
    }
});

// Using Lambda
fruits.forEach(fruit -> System.out.println(fruit));
```

## 🏗 Stream API
Streams allow functional-style operations on collections.

```java
import java.util.stream.Collectors;

List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

// Filter even numbers and multiply by 2
List<Integer> evenSquares = list.stream()
    .filter(n -> n % 2 == 0)
    .map(n -> n * n)
    .collect(Collectors.toList());

evenSquares.forEach(System.out::println);
```

## 🏗 Functional Interfaces
A functional interface contains exactly one abstract method.
- `Predicate<T>`: Returns a boolean.
- `Function<T, R>`: Returns a value.
- `Consumer<T>`: Returns nothing.
- `Supplier<T>`: Supplies a value.

```java
import java.util.function.*;

Predicate<Integer> isEven = n -> n % 2 == 0;
Function<Integer, Integer> square = n -> n * n;
Consumer<String> showMsg = s -> System.out.println(s);
Supplier<Double> getPI = () -> 3.14159;
```

## 🏗 Date & Time API (`java.time`)
A modern, immutable, and thread-safe way to handle date and time.
```java
import java.time.LocalDate;
import java.time.LocalTime;
import java.time.LocalDateTime;

LocalDate date = LocalDate.now();
LocalTime time = LocalTime.now();
LocalDateTime dateTime = LocalDateTime.now();
```

---
[⬅ Back to Roadmap](../README.md)
