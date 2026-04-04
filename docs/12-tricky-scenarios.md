# 12 - Tricky Interview Scenarios & Edge Cases

When interviewing for Senior Java positions, you will often encounter code snippets designed to test your deep understanding of JVM internals, compile-time vs runtime behavior, and API quirks.

Here is the complete curated list of the most notorious Java interview traps.

---

## 🏗 1. String Pool & Immutability Traps

### Compile-time vs Runtime Concatenation
```java
String a = "hello";
String b = "he" + "llo";
String c = new String("hello");
String d = "he" + new String("llo");

System.out.println(a == b); // true. b is a compile-time constant, optimized to pool.
System.out.println(a == c); // false. 'new' creates a distinct object on the heap.
System.out.println(a == d); // false. Runtime concatenation creates a new object on heap.
```

### Immutability & Reassignment
```java
String s = "hello";
s.concat(" world");
System.out.println(s); // Output: hello (Strings are immutable, result was not re-assigned)
```

### String `intern()` Behavior
```java
String s1 = new String("a") + new String("b"); // Heap
String s2 = "ab"; // Pool
System.out.println(s1 == s2); // false

s1 = s1.intern(); // Reassigns s1 to the pooled reference
System.out.println(s1 == s2); // true
```

---

## 🏗 2. Exceptional Flow & `finally`

### `finally` Overrides `return`
```java
public static int test() {
    try { return 1; } finally { return 2; }
}
// Outputs: 2 (finally totally overrides the try return)
```

### Variable Updates in `finally`
```java
public static int test() {
    int x = 1;
    try {
        x = 2; return x;
    } finally {
        x = 4; // This changes x, but DOES NOT change the returned value
    }
}
// Outputs: 2. The return value is evaluated and stored before finally executes.
```

### Exception Masking
```java
try {
    throw new RuntimeException("try");
} finally {
    throw new RuntimeException("finally");
}
// Only throws "finally". The original exception from 'try' is swallowed and lost.
```

### JVM Termination
```java
try {
    System.exit(0);
} finally {
    System.out.println("finally"); // NEVER EXECUTES. System.exit kills the JVM instantly.
}
```

---

## 🏗 3. Object & Primitive Caching Traps

### The Integer Cache
```java
Integer a = 127; Integer b = 127;
System.out.println(a == b); // true

Integer c = 128; Integer d = 128;
System.out.println(c == d); // false
```
**Explanation:** Java caches `Integer` objects from `-128` to `127` to save memory. 128 falls outside this cache, resulting in two distinct heap objects.

### Evaluation Order (`++` Tricks)
```java
int i = 1;
i = i++ + ++i;
System.out.println(i); // Output: 4
```
**Explanation:** Java evaluates expressions stringently from left to right.
1. `i++` evaluates first. It returns the current value of `i` (**1**) for the addition, and then increments `i` to **2**.
2. `++i` evaluates next. It sees `i` is now 2, increments it immediately to **3**, and returns **3** for the addition.
3. The addition resolves as `1 + 3 = 4`.
4. The final assignment to `i` (`i = ...`) overrides the intermediary increments, definitively setting `i` to **4**.

---

## 🏗 4. Collections API Quirks

### Array vs List Equality
```java
int[] a = {1, 2, 3}; int[] b = {1, 2, 3};
System.out.println(a.equals(b)); // false (Arrays use Object.equals, memory ref)

List<Integer> l1 = Arrays.asList(1, 2, 3); List<Integer> l2 = Arrays.asList(1, 2, 3);
System.out.println(l1.equals(l2)); // true (Lists override equals)
```

### The List `remove` Overload Trap
```java
List<Integer> list = new ArrayList<>();
list.add(1); list.add(2); list.add(3);
list.remove(1); // Removes by **INDEX**, not value! So it removes the '2' at index 1.
System.out.println(list); // [1, 3]
```

### HashMap Null Keys & Mutable Objects
- **Null Keys**: HashMap allows exactly **one** null key. Overwriting it replaces the value. `map.put(null, "A"); map.put(null, "B");` -> `{null=B}`.
- **Mutable Objects**: Never use a mutable object as a HashMap key. If `keyObj.id` changes after `map.put()`, its `hashCode` changes. `map.get(keyObj)` will return `null` because it searches the wrong bucket.

### HashSet Duplicates without `equals()`
If you add two identical custom objects (e.g. `new Person(1)`) to a `HashSet` but the `Person` class does not override `hashCode()` and `equals()`, the Set will treat them as different memory references and the Set size will be `2`.

### Remove in Loop (`ConcurrentModificationException`)
```java
for (Integer i : list) {
    if (i % 2 == 0) list.remove(i); // THROWS ConcurrentModificationException!
}
```
**Fix:** Use `Iterator.remove()` or Java 8 `list.removeIf(i -> i % 2 == 0)`.

---

## 🏗 5. Core Class Initialization & JVM

### Class Loading vs Instantiation
```java
class Test { static { System.out.println("Static"); } }
Test t = null; // Outputs nothing. Declaring a variable does NOT trigger class loading.
```

### Overloading vs Overriding Resolution
```java
class A { void show(Object o) { System.out.println("Object"); } }
class B extends A { void show(String s) { System.out.println("String"); } }

A obj = new B();
obj.show("hello"); // Output: Object
```
**Explanation:** Method overloading is resolved at **compile-time**. The compiler sees `obj` as Type `A` and actively binds it to the only method it knows: `A.show(Object)`. Runtime overriding does not retroactively change the overloaded signature chosen.

### Enum Constructors
Enums are initialized completely upon class loading. If it has values `A, B`, the constructor will run exactly twice immediately when the Enum is accessed for the first time.

### Interface Default Methods (Diamond Problem)
If a class implements `Interface A` and `Interface B` which both supply a default method `show()`, you get a **compile-time error**. It must be manually overridden to avoid ambiguity: `A.super.show()`.

---

## 🏗 6. Advanced APIs (Threads, Streams, Optional)

### Thread `.run()` vs `.start()`
Calling `t.run()` executes the thread code synchronously on the current main thread. It does not spawn a background thread. You must use `t.start()`.

### Deadlocks
A deadlock happens when Thread 1 locks Resource A and waits for B, while Thread 2 locks Resource B and waits for A. **Solution**: Always acquire nested locks in a consistent, alphabetical/numerical order across all threads.

### Stream Laziness
```java
Stream.of(1, 2).filter(x -> { System.out.println(x); return true; });
```
**Explanation:** Displays **nothing**. Streams are completely lazy and do not execute intermediate operations unless a terminal operation (like `.collect()` or `.count()`) is invoked.

### Optional Trap
```java
Optional<String> opt = Optional.of(null); // THROWS NullPointerException!
```
**Fix**: Always use `Optional.ofNullable(null)` if there's a chance the value is not present.
