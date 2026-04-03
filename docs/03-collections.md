# 03 - Java Collections Framework

The Collections framework provides a standardized way to store and manipulate groups of objects.

---

## 🏗 The Core Interfaces
- **List**: An ordered collection that allows duplicate elements.
- **Set**: A collection of unique elements.
- **Map**: A key-value pair collection.

### 🖇 List
```java
List<String> fruits = new ArrayList<>();
fruits.add("Apple");
fruits.add("Banana");
fruits.add("Apple"); // Duplicates allowed
```

### 🖇 Set
```java
Set<Integer> uniqueNumbers = new HashSet<>();
uniqueNumbers.add(10);
uniqueNumbers.add(20);
uniqueNumbers.add(10); // Duplicate will be ignored
```

### 🖇 Map
```java
Map<String, String> capitalMap = new HashMap<>();
capitalMap.put("USA", "Washington D.C.");
capitalMap.put("India", "New Delhi");
```

## 🧩 Generics
Generics make code type-safe and more robust.
```java
// Without Generics (Can lead to Runtime errors)
List numbers = new ArrayList();
numbers.add(1);
numbers.add("Not a number");

// With Generics
List<Integer> intList = new ArrayList<>();
intList.add(1);
// intList.add("String"); // Compile-time error!
```

## 🔁 Iterators
Used to traverse a collection of objects one by one.
```java
Iterator<String> it = fruits.iterator();
while (it.hasNext()) {
    System.out.println(it.next());
}
```

---
[⬅ Back to Roadmap](../README.md)
