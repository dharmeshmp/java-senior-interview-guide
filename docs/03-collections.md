# 03 - Collections API Deep Dive

A senior Java developer must know exactly what happens inside data structures during high concurrency, resizing, and iterations.

---

## 🏗 HashMap Internals
A `HashMap` uses an Array of Nodes (`Map.Entry`).
- **Hashing**: Computes `hash(key) ^ (hash(key) >>> 16)` to minimize collisions, then assigns a bucket via bitmasking: `(n - 1) & hash`.
- **Treeification (Java 8)**: To defend against hash collision attacks (DoS), when a bucket has more than `TREEIFY_THRESHOLD` (8) items and the array length is $\ge$ 64, the linked list is converted to a Red-Black Tree. This drops lookup time from $O(N)$ to $O(\log N)$.
- **Load Factor & Resizing**: Default load factor is `0.75`. When entries exceed capacity * load factor, it doubles the array size and rehashes all elements.

## 🏗 Concurrent Collections
### `ConcurrentHashMap`
- **Java 7 vs Java 8**: Java 7 used Segment Locking (array of segments, locking a specific part). Java 8 abandoned segments. It uses the `Node` array and locks only the specific bucket's head node using `synchronized` and `Compare-And-Swap (CAS)` operations, allowing massive concurrent writes.

### `CopyOnWriteArrayList`
- Useful for read-heavy operations where writes are rare (e.g., Listener lists).
- On every write/add/remove, the entire underlying array is copied. This guarantees thread-safety without explicit locking during reads.

## 🏗 Comparable vs Comparator
Both interfaces are used to sort collections, but their design purposes and use-cases are fundamentally different.

### 1. `Comparable` (java.lang)
Modifies the class itself to define its **natural ordering**. You implement `Comparable<T>` on the class itself and override `compareTo(T obj)`.
- **Limitation**: You can only sort by one property (e.g., sort `Employee` by ID). If you don't own the source code of the class, you cannot use Comparable.
```java
class Employee implements Comparable<Employee> {
    int id; String name;
    
    // Sort by ID naturally
    @Override
    public int compareTo(Employee other) {
        return Integer.compare(this.id, other.id);
    }
}
// Usage
Collections.sort(employeeList);
```

### 2. `Comparator` (java.util)
Defines a **custom ordering** completely external to the class. You implement `Comparator<T>` by overriding `compare(T obj1, T obj2)`.
- **Advantage**: Highly flexible. You can have multiple logic to sort (by Salary, by Name, or by Age) and easily pass them to `Collections.sort()` without modifying the `Employee` class.
```java
class Employee { int id; String name; int salary; }

// Usage with Java 8 Lambdas
Comparator<Employee> byName = (e1, e2) -> e1.name.compareTo(e2.name);
Collections.sort(employeeList, byName);

// Usage with Method References (Cleaner)
Collections.sort(employeeList, Comparator.comparing(Employee::getSalary));
```

## 🏗 Fail-Fast vs Fail-Safe Iterators
- **Fail-Fast** (e.g., `ArrayList`, `HashMap`): Uses a `modCount` variable. If the collection is structurally modified during iteration via a different thread/iterator, it throws `ConcurrentModificationException`.
- **Fail-Safe** (e.g., `ConcurrentHashMap`, `CopyOnWriteArrayList`): Iterates over a clone or weakly consistent view of the collection. It will not throw `ConcurrentModificationException` and allows modifications during iteration.

### Interview Questions

**Q1: Set vs List?**
- **List**: An ordered collection (maintains insertion order). Allows duplicate elements. Allows multiple `null` values.
- **Set**: An unordered collection (does not guarantee insertion order, exception is `LinkedHashSet`). Does not allow duplicates. Allows only one `null` value.

**Q2: When to use ArrayList vs LinkedList?**
- `ArrayList`: Backed by a dynamic array. Excellent for random access (`O(1)` time) and trailing insertions. Bad for insertions/deletions in the middle of the list (`O(N)`) because all subsequent elements must be shifted in memory.
- `LinkedList`: Backed by a doubly-linked list. Good for insertions/deletions at the ends or middle (if iterator is already there, `O(1)`). Bad for random access (`O(N)`).

**Q3: Explain HashMap internal working.**
As described above, it uses an array of node linked-lists. When `put(K, V)` is called, it calculates the hash of `K`, applies bit masking to find the array index (bucket). If a collision occurs (two different keys, same bucket), the new node is appended to the linked list. In Java 8+, if the linked list length reaches 8, it converts to a Red-Black tree for $O(\log N)$ search time.

**Q4: What is an IdentityHashMap?**
A special implementation of `Map` that uses reference equality (`==`) instead of object equality (`equals()`) when comparing keys. It's often used by garbage collectors or object graph traversers to track visited objects accurately.

**Q5: How do you sort a HashMap by its values?**
Since a `HashMap` is unordered, you must extract the entries (`entrySet()`), put them into a List, sequence them using a custom comparator, and insert the sorted result into a `LinkedHashMap` (which maintains insertion order).
```java
Map<String, Integer> sortedMap = map.entrySet()
    .stream()
    .sorted(Map.Entry.comparingByValue())
    .collect(Collectors.toMap(
        Map.Entry::getKey, 
        Map.Entry::getValue, 
        (e1, e2) -> e1, 
        LinkedHashMap::new));
```

**Q6: When would you use `TreeMap` over `HashMap`?**
Use `TreeMap` when you need keys sorted in a specific natural or custom order (NavigableMap/SortedMap). However, operations are $O(\log N)$ instead of $O(1)$.


---
[⬅ Back to Interview Roadmap](../README.md)
