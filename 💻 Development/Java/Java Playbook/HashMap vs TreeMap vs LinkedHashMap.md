---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Java
difficulty: Intermediate
tags:
  - collections
  - hashmap
  - treemap
  - linkedhashmap
aliases:
  - Map Implementations Compared
  - Sorted Map Java
publish: true
permalink: java/hashmap-vs-treemap-vs-linkedhashmap
---

# <span class="rune">ᛟ</span> HashMap vs TreeMap vs LinkedHashMap

> *All three answer "give me the value for this key" equally well. What actually separates them is a question nobody asks until it matters: what order do you get the keys back in when you ask for all of them?*

---

## 🎯 Purpose

`HashMap`, `TreeMap`, and `LinkedHashMap` all implement the `Map` interface — same core key-value contract — but differ in iteration order and lookup performance guarantees. The choice between them almost always comes down to one question: does iteration order matter, and if so, does it need to be insertion order or sorted order?

---

## 🧠 Key Ideas

- `HashMap` provides **no guaranteed iteration order at all** — the order keys come back in when iterating is an implementation detail that can even change between runs, in exchange for the best average-case performance: `O(1)` for `get`/`put`/`remove`.
- `LinkedHashMap` maintains **insertion order** (the order keys were first added) — internally it's a `HashMap` with an additional linked list threading through entries to preserve that order, at a small memory/performance cost over plain `HashMap`.
- `TreeMap` maintains keys in **sorted order** (natural ordering via `Comparable`, or a custom `Comparator` supplied at construction) — implemented as a red-black tree, giving `O(log n)` for `get`/`put`/`remove` rather than `HashMap`'s `O(1)`.
- All three require a properly implemented `equals()`/`hashCode()` pair on the key type for `HashMap`/`LinkedHashMap` (see [[Object Equality - equals(), ==, and hashCode()]]) — `TreeMap` instead requires the key type to implement `Comparable`, or a `Comparator` to be provided.
- `TreeMap` additionally provides navigation methods beyond the basic `Map` interface — `firstKey()`, `lastKey()`, `higherKey()`, `floorKey()`, `subMap()` — for range-based queries that neither `HashMap` nor `LinkedHashMap` support at all.

---

## ⚙️ How It Works

`HashMap` computes a hash code for each key (see [[Object Equality - equals(), ==, and hashCode()]]) to determine which internal bucket it belongs in, giving near-constant-time lookup regardless of the map's size — but that hash-based bucket placement has no relationship whatsoever to insertion order or any natural ordering, which is exactly why iteration order is unpredictable. `LinkedHashMap` extends this same hash-based structure but additionally threads a doubly-linked list through every entry in the order they were inserted, letting iteration follow that separate list rather than the unpredictable bucket layout. `TreeMap` abandons hashing entirely in favor of a self-balancing binary search tree keyed by comparison order, trading `HashMap`'s faster average lookup for guaranteed sorted iteration and range-query support.

```text
HashMap:       bucket-based, NO reliable order — fastest average lookup
LinkedHashMap: HashMap + a linked list tracking INSERTION order — small overhead, predictable order
TreeMap:       red-black tree, SORTED order always — O(log n) lookup, range queries supported
```

---

## 💻 Examples

```java
Map<String, Integer> hashMap = new HashMap<>();
hashMap.put("banana", 2);
hashMap.put("apple", 1);
hashMap.put("cherry", 3);
System.out.println(hashMap); // order is unpredictable — don't rely on it

Map<String, Integer> linkedMap = new LinkedHashMap<>();
linkedMap.put("banana", 2);
linkedMap.put("apple", 1);
linkedMap.put("cherry", 3);
System.out.println(linkedMap); // {banana=2, apple=1, cherry=3} — guaranteed insertion order

Map<String, Integer> treeMap = new TreeMap<>();
treeMap.put("banana", 2);
treeMap.put("apple", 1);
treeMap.put("cherry", 3);
System.out.println(treeMap); // {apple=1, banana=2, cherry=3} — always sorted by key

// TreeMap's extra navigation methods
TreeMap<String, Integer> tm = new TreeMap<>(treeMap);
System.out.println(tm.firstKey());       // "apple"
System.out.println(tm.lastKey());        // "cherry"
System.out.println(tm.higherKey("apple")); // "banana" — the next key greater than "apple"

// Custom Comparator for descending order
Map<String, Integer> descending = new TreeMap<>(Comparator.reverseOrder());
descending.putAll(treeMap);
System.out.println(descending); // {cherry=3, banana=2, apple=1}
```

---

## 🚀 Real World Applications

- `HashMap` as the default choice for the vast majority of key-value lookup needs where order simply doesn't matter
- `LinkedHashMap` for caches or configurations where predictable, insertion-order iteration is needed for output or logging consistency
- `LinkedHashMap` (with its access-order constructor option) as the standard building block for implementing an LRU (least-recently-used) cache
- `TreeMap` for anything requiring sorted iteration, range queries ("all entries between X and Y"), or finding the nearest key above/below a given value

---

## ⚖️ Advantages

**HashMap:** fastest average-case performance for pure key lookup, no ordering overhead at all.
**LinkedHashMap:** predictable insertion-order iteration with only a small overhead over plain `HashMap`.
**TreeMap:** guaranteed sorted order plus powerful range/navigation queries no hash-based map can offer.

---

## ⚠️ Limitations

- `HashMap`'s lack of ordering guarantee means code that happens to "look ordered" during testing can break unpredictably later — never rely on `HashMap` iteration order for anything.
- `TreeMap`'s `O(log n)` operations are meaningfully slower than `HashMap`'s `O(1)` average case for pure lookup-heavy workloads with no sorting need.
- `TreeMap` requires keys to be mutually comparable (via `Comparable` or a supplied `Comparator`) — attempting to insert a key type that isn't comparable, with no comparator provided, throws a `ClassCastException` at runtime.
- `LinkedHashMap`'s extra linked-list bookkeeping adds a small memory and performance cost over plain `HashMap`, which is wasted if insertion order was never actually needed.

---

## 🚨 Common Mistakes

- Assuming `HashMap` iterates in insertion order because it happened to look that way in a small test case — this is not guaranteed and can differ across JVM versions, map sizes, or even multiple runs of the same program.
- Using `TreeMap` by default "just in case sorting is needed later," paying its `O(log n)` cost for a use case that never actually needed sorted iteration.
- Forgetting `TreeMap` needs `Comparable` keys or an explicit `Comparator` — inserting an incomparable key type throws a runtime `ClassCastException`, not a compile-time error.
- Choosing `HashMap` for an LRU cache implementation instead of `LinkedHashMap`, missing its built-in access-order mode that makes implementing LRU eviction dramatically simpler.

---

## 📖 Further Reading

- Oracle documentation: `java.util.HashMap`, `java.util.TreeMap`, `java.util.LinkedHashMap` class references
- Oracle documentation: `java.util.NavigableMap` (the interface providing `TreeMap`'s range/navigation methods)

---

## 💡 Wisdom from Mímir

Ask exactly one question before choosing: does the order I get keys back in actually matter to this code? If no, `HashMap`. If yes and it should match insertion order, `LinkedHashMap`. If yes and it should be sorted, `TreeMap`. Skipping this question is how a `TreeMap`'s sorting overhead ends up paid for on data that was never going to be iterated in order anyway.

---

## 🔗 Related Notes

- [[The Collections Framework Overview]]
- [[Object Equality - equals(), ==, and hashCode()]]
- [[Comparable vs Comparator]]
- [[HashSet vs TreeSet vs LinkedHashSet]]
- [[Java Codex]]
