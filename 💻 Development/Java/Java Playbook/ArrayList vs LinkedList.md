---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Java
difficulty: Intermediate
tags:
  - collections
  - arraylist
  - linkedlist
  - performance
aliases:
  - ArrayList Performance
  - LinkedList Performance
publish: true
permalink: java/arraylist-vs-linkedlist
---

# <span class="rune">ᛟ</span> ArrayList vs LinkedList

> *ArrayList is a resizable array pretending to be flexible. LinkedList is a chain of independent nodes pretending to be indexed. Each pays for what the other gives away for free.*

---

## 🎯 Purpose

Both `ArrayList` and `LinkedList` implement the `List` interface — same contract, same basic behavior from the outside — but their underlying data structures produce genuinely different performance characteristics for different operations. Choosing between them is a textbook example of a data structure tradeoff decision: fast random access versus fast insertion/removal at arbitrary positions.

---

## 🧠 Key Ideas

- `ArrayList` is backed by a **resizable array** — elements are stored contiguously, and accessing any index is a direct, constant-time (`O(1)`) array lookup.
- `LinkedList` is backed by a **doubly-linked list** of nodes — each node holds a reference to the next and previous node, and accessing an arbitrary index requires walking the chain from one end (`O(n)`).
- Inserting or removing at the **beginning or middle** of an `ArrayList` requires shifting every subsequent element over by one position (`O(n)`); the same operation on a `LinkedList` is `O(1)` **once you already have a reference to the node** — but finding that node in the first place is still `O(n)`.
- `ArrayList`'s backing array grows by allocating a new, larger array and copying elements over whenever it runs out of capacity — an occasional `O(n)` cost, amortized to effectively `O(1)` per `add()` on average.
- `LinkedList` also implements `Deque`, making it a natural choice specifically when frequent additions/removals at *both ends* are needed (like a queue or stack), rather than arbitrary middle-of-list operations.

---

## ⚙️ How It Works

An `ArrayList`'s contiguous memory layout is exactly why indexed access (`get(i)`) is so fast — the JVM can compute the exact memory address of any element directly from its index, with no traversal needed at all. That same contiguous layout is precisely why inserting into the middle is expensive: every element after the insertion point has to physically shift over by one slot to make room. A `LinkedList`'s nodes, scattered independently across the heap and connected only by references, flip this tradeoff entirely — inserting a node just means relinking a couple of pointers (fast), but finding *which* node to insert next to requires walking the chain one link at a time from an end (slow for arbitrary positions).

```text
ArrayList:  [A][B][C][D][E]   ← contiguous — get(2) is instant, insert(2, X) shifts C,D,E right

LinkedList: A <-> B <-> C <-> D <-> E   ← scattered nodes — get(2) walks A→B→C,
                                            insert next to a KNOWN node is instant
```

---

## 💻 Examples

```java
List<Integer> arrayList = new ArrayList<>();
List<Integer> linkedList = new LinkedList<>();

// Fast for ArrayList — O(1) direct indexed access
for (int i = 0; i < 100_000; i++) arrayList.add(i);
int value = arrayList.get(50_000); // instant

// Slow for LinkedList — O(n), must walk from an end to reach index 50,000
for (int i = 0; i < 100_000; i++) linkedList.add(i);
int value2 = linkedList.get(50_000); // walks ~50,000 nodes

// Fast for LinkedList as a Deque — O(1) at both ends
LinkedList<Integer> deque = new LinkedList<>();
deque.addFirst(1);   // instant
deque.addLast(2);    // instant
deque.removeFirst(); // instant

// Slow for ArrayList doing the same thing at the front
List<Integer> list = new ArrayList<>(List.of(1, 2, 3));
list.add(0, 99); // O(n) — everything shifts right by one to make room
```

---

## 🚀 Real World Applications

- `ArrayList` for the vast majority of everyday list use — reading, iterating, and appending to the end are all common and all fast
- `LinkedList` (or, more commonly today, `ArrayDeque`) when implementing a queue or stack with frequent additions/removals at both ends
- `ArrayList` when random access by index is a core requirement (binary search, sorting algorithms, direct index-based lookups)
- Avoiding `LinkedList` purely as a "default" list choice — in modern practice, `ArrayList` is the correct default nearly all the time, with `LinkedList`/`ArrayDeque` reserved for the specific deque-shaped use case

---

## ⚖️ Advantages

**ArrayList:**
- Fast constant-time indexed access and iteration
- Lower memory overhead per element (no per-node pointer overhead)
- Better CPU cache locality due to contiguous memory layout

**LinkedList:**
- Fast constant-time insertion/removal at both ends, or anywhere given an existing node reference
- No need to resize/copy an entire backing array as the list grows
- Naturally supports `Deque` operations (stack- and queue-like usage)

---

## ⚠️ Limitations

- `ArrayList` insertion/removal anywhere but the very end requires shifting elements, which is genuinely expensive for large lists with frequent mid-list modifications.
- `LinkedList` indexed access (`get(i)`) is genuinely slow for large lists — an `O(n)` operation disguised behind the same `List` interface method signature as `ArrayList`'s `O(1)` version, which is easy to overlook.
- `LinkedList`'s per-node overhead (two extra references per element, for `next`/`previous`) uses noticeably more memory than `ArrayList`'s tightly packed array for the same number of elements.

---

## 🚨 Common Mistakes

- Choosing `LinkedList` reflexively (a very common habit from academic data structures courses) without an actual need for frequent middle/end insertions, when `ArrayList` would perform better for typical usage patterns.
- Iterating a `LinkedList` with an indexed `for` loop (`for (int i = 0; i < list.size(); i++) list.get(i)`), which is `O(n²)` overall — each `.get(i)` call independently re-walks the chain from the start; an iterator or enhanced for-each loop avoids this entirely.
- Assuming `LinkedList`'s `O(1)` insertion applies to inserting at an arbitrary *index* — that's still `O(n)` overall, since finding the correct node to insert next to requires walking the list first; only insertion at an *already-held* node reference (or the very ends) is genuinely `O(1)`.
- Not considering `ArrayDeque` as a often-better-performing alternative to `LinkedList` for pure queue/stack use cases, since `ArrayDeque` avoids per-node pointer overhead entirely.

---

## 📖 Further Reading

- Oracle documentation: `java.util.ArrayList`, `java.util.LinkedList` class references
- Oracle documentation: `java.util.ArrayDeque` (often the better queue/stack choice)

---

## 💡 Wisdom from Mímir

Default to `ArrayList` unless a specific, demonstrated need points to `LinkedList` — frequent insertion/removal at both ends being the main one, and even then, `ArrayDeque` often performs better for that exact use case. `LinkedList`'s appeal is mostly theoretical; in practice, `ArrayList`'s cache-friendly contiguous memory usually wins even for many operations the textbook Big-O comparison suggests should favor `LinkedList`.

---

## 🔗 Related Notes

- [[The Collections Framework Overview]]
- [[Arrays in Java]]
- [[Iterators and the Iterable Interface]]
- [[Java Codex]]
