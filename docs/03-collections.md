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

## 🏗 Fail-Fast vs Fail-Safe Iterators
- **Fail-Fast** (e.g., `ArrayList`, `HashMap`): Uses a `modCount` variable. If the collection is structurally modified during iteration via a different thread/iterator, it throws `ConcurrentModificationException`.
- **Fail-Safe** (e.g., `ConcurrentHashMap`, `CopyOnWriteArrayList`): Iterates over a clone or weakly consistent view of the collection. It will not throw `ConcurrentModificationException` and allows modifications during iteration.

### Interview Question
*When would you use `TreeMap` over `HashMap`?*
Use `TreeMap` when you need keys sorted in a specific order (NavigableMap/SortedMap). However, operations are $O(\log N)$ instead of $O(1)$.

---
[⬅ Back to Interview Roadmap](../README.md)
