# 12 - Tricky Interview Scenarios & Edge Cases

When interviewing for Senior Java positions, you will often encounter code snippets designed to test your deep understanding of JVM internals, compile-time vs runtime behavior, and API quirks.

Here is a curated list of the most notorious Java interview traps.

---

## 🏗 1. String Pool & Immutability Traps

### Q1: Compile-time vs Runtime Concatenation
```java
String a = "hello";
String b = "he" + "llo";
String c = new String("hello");
String d = "he" + new String("llo");

System.out.println(a == b); // true
System.out.println(a == c); // false
System.out.println(a == d); // false
```
**Explanation:** `b` is a compile-time constant, so the compiler optimizes it to `"hello"` and retrieves it from the String Pool. `c` and `d` involve the `new` keyword, which guarantees object creation on the Heap outside the pool.

### Q2: Immutability & Reassignment
```java
String s = "hello";
s.concat(" world");
System.out.println(s); // Output: hello
```
**Explanation:** Strings are immutable. `concat()` returns a completely new String object, but since it wasn't assigned to a variable (e.g., `s = s.concat(...)`), the original `s` remains unchanged.

### Q3: String `intern()` Advanced
```java
String s1 = new String("a") + new String("b");
String s2 = "ab";
System.out.println(s1 == s2); // false

s1 = s1.intern();
System.out.println(s1 == s2); // true
```
**Explanation:** `intern()` checks the String Pool. If the string value already exists, it returns a reference to the pooled object. 

---

## 🏗 2. Exceptional Flow & `finally`

### Q4: `finally` Overrides Everything
```java
public static int test() {
    try {
        return 1;
    } finally {
        return 2;
    }
}
System.out.println(test()); // Output: 2
```
**Explanation:** A `return` within a `finally` block suppresses any previous `return` values or thrown exceptions from the `try` or `catch` block.

### Q5: Exception Masking
```java
try {
    throw new RuntimeException("try");
} finally {
    throw new RuntimeException("finally");
}
```
**Explanation:** The program throws `RuntimeException: finally`. The original exception from the `try` block is completely swallowed and lost (unless added as a suppressed exception natively like in try-with-resources).

### Q6: JVM Termination (`System.exit(0)`)
```java
try {
    System.out.println("try");
    System.exit(0);
} finally {
    System.out.println("finally");
}
```
**Explanation:** The output is only `try`. The `finally` block is **never** executed because `System.exit(0)` forcefully terminates the JVM.

---

## 🏗 3. Core Class Initialization & Execution

### Q7: Class Loading vs Instantiation
```java
class Test {
    static { System.out.println("Static block"); }
}

public static void main(String[] args) {
    Test t = null;
}
```
**Explanation:** **No output**. Declaring a variable does not load the class. The class is only loaded (and the static block executed) when it is instantiated, a static method is called, or a static field is accessed.

### Q8: Sequence of Initialization
```java
class Test {
    static { System.out.println("Static"); }
    { System.out.println("Instance block"); }
    public Test() { System.out.println("Constructor"); }
}
// Calling new Test(); new Test();
```
**Explanation:** 
`Static` (runs once per class loading)
`Instance block` (runs before every constructor)
`Constructor`
`Instance block`
`Constructor`

### Q9: Enum Constructors
```java
enum Test {
    A, B;
    Test() { System.out.println("Constructor"); }
}
public static void main(String[] args) {
    Test t = Test.A;
}
```
**Explanation:** Output is:
`Constructor`
`Constructor`
Enums are initialized completely upon the class loading. Every defined constant (`A`, `B`) gets instantiated immediately invoking the constructor once per constant.

---

## 🏗 4. Collections API Traps

### Q10: Array vs List Equality
```java
int[] a = {1, 2, 3};
int[] b = {1, 2, 3};
System.out.println(a.equals(b)); // false

List<Integer> l1 = Arrays.asList(1, 2, 3);
List<Integer> l2 = Arrays.asList(1, 2, 3);
System.out.println(l1.equals(l2)); // true
```
**Explanation:** Arrays in Java do not override `equals()`, so it relies on `Object.equals` (reference comparison). Lists override `equals()` to compare collection contents. Use `Arrays.equals(a, b)` for arrays.

### Q11: The List `remove` Overload Trap
```java
List<Integer> list = new ArrayList<>();
list.add(1); list.add(2); list.add(3);
list.remove(1);
System.out.println(list); // [1, 3]
```
**Explanation:** `remove(1)` triggers the `remove(int index)` method, NOT `remove(Object o)`. Therefore, it removes the element at index 1 (which is `2`). If you wanted to remove the value `1`, you must pass an object: `list.remove(Integer.valueOf(1))`.

### Q12: Mutable Objects as HashMap Keys
If an object is used as a `HashMap` key, and its *mutated* after insertion, its HashCode changes.
```java
// If 'Key' is mutable and its fields change:
map.put(keyObj, "value");
keyObj.id = 2; // Changes hashcode!
map.get(keyObj); // Returns NULL (looks in the wrong bucket)
```
**Explanation:** **Never** use mutable objects as `HashMap` keys. Always use immutable objects like `String` or `Integer`.

---

## 🏗 5. Advanced OOP & Threads

### Q13: Overloading is Compile-Time
```java
class A { void show(Object o) { System.out.println("Object"); } }
class B extends A { void show(String s) { System.out.println("String"); } }

A obj = new B();
obj.show("hello");
```
**Explanation:** Output is `Object`. Method overriding is resolved at runtime (dynamic dispatch), but **method overloading is resolved at compile time** (static dispatch). The compiler sees `obj` as Type `A` and binds to `A.show(Object)`.

### Q14: Default Interface Methods (Diamond Problem)
```java
interface A { default void show() {} }
interface B { default void show() {} }
class Test implements A, B { } // Compile-Time Error!
```
**Explanation:** A class inheriting unrelated defaults from multiple interfaces gets a compile-time ambiguity error. You must manually override the method: `public void show() { A.super.show(); }`.

### Q15: Thread `.run()` vs `.start()`
```java
Thread t = new Thread(() -> System.out.println("Async"));
t.run();
```
**Explanation:** Prints `Async` but does **not** create a new OS thread. Calling `run()` directly merely executes the method synchronously in the current main thread execution context. You must call `start()` to trigger multithreading.
