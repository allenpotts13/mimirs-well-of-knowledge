---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Java
difficulty: Intermediate
tags:
  - collections
  - hashset
  - treeset
  - linkedhashset
aliases:
  - Set Implementations Compared
  - Sorted Set Java
publish: true
permalink: java/hashset-vs-treeset-vs-linkedhashset
---

# <span class="rune">ᛟ</span> HashSet vs TreeSet vs LinkedHashSet

> *Every Set guarantees no duplicates — that part is never in question. What actually decides which one you want is exactly the same question that decides which Map you want, because two of these three are literally built on top of the Map versions.*

---

## 🎯 Purpose

`HashSet`, `TreeSet`, and `LinkedHashSet` all implement the `Set` interface, guaranteeing no duplicate elements — but each is built internally on top of one of the Map implementations covered in [[HashMap vs TreeMap vs LinkedHashMap]], and inherits that same map's ordering and performance characteristics. Choosing between them follows the identical logic as choosing between the corresponding Maps, just applied to a collection of standalone elements instead of key-value pairs.

---

## 🧠 Key Ideas

- `HashSet` is internally backed by a `HashMap` (storing elements as keys, with a dummy placeholder value) — no guaranteed iteration order, but the fastest average-case `add`/`remove`/`contains` at `O(1)`.
- `LinkedHashSet` is backed by a `LinkedHashMap` — maintains insertion order during iteration, at a small overhead over `HashSet`.
- `TreeSet` is backed by a `TreeMap` — maintains elements in sorted order (natural via `Comparable`, or a supplied `Comparator`), with `O(log n)` operations and the same range/navigation methods (`first()`, `last()`, `higher()`, `floor()`) that `TreeMap` offers for keys.
- Just as with `HashMap`, elements stored in a `HashSet`/`LinkedHashSet` need a correct `equals()`/`hashCode()` pair (see [[Object Equality - equals(), ==, and hashCode()]]) — a broken implementation can let genuine duplicates slip in, or cause a genuinely-present element to appear "missing" from `.contains()`.
- `TreeSet` requires elements to be mutually comparable, exactly like `TreeMap` requires comparable keys — either implementing `Comparable` or supplying a `Comparator` at construction.

---

## ⚙️ How It Works

Because each `Set` implementation is a thin wrapper around its corresponding `Map` implementation — using elements as keys and ignoring the values entirely — every performance and ordering characteristic transfers directly: `HashSet` inherits `HashMap`'s unordered, `O(1)`-average bucket-based lookup; `LinkedHashSet` inherits `LinkedHashMap`'s insertion-order-preserving linked list; `TreeSet` inherits `TreeMap`'s sorted red-black tree structure and `O(log n)` operations.

```text
HashSet       → wraps a HashMap<E, Object>       — no order, fastest average lookup
LinkedHashSet → wraps a LinkedHashMap<E, Object>  — insertion order preserved
TreeSet       → wraps a TreeMap<E, Object>        — sorted order, range queries supported
```

---

## 💻 Examples

```java
Set<String> hashSet = new HashSet<>();
hashSet.add("banana");
hashSet.add("apple");
hashSet.add("cherry");
hashSet.add("apple");            // duplicate — silently ignored, size stays 3
System.out.println(hashSet);     // order is unpredictable

Set<String> linkedSet = new LinkedHashSet<>();
linkedSet.add("banana");
linkedSet.add("apple");
linkedSet.add("cherry");
System.out.println(linkedSet);   // [banana, apple, cherry] — guaranteed insertion order

Set<String> treeSet = new TreeSet<>();
treeSet.add("banana");
treeSet.add("apple");
treeSet.add("cherry");
System.out.println(treeSet);     // [apple, banana, cherry] — always sorted

// TreeSet's navigation methods
TreeSet<Integer> numbers = new TreeSet<>(Set.of(10, 20, 30, 40));
System.out.println(numbers.first());        // 10
System.out.println(numbers.higher(20));     // 30 — smallest element strictly greater than 20
System.out.println(numbers.floor(25));      // 20 — largest element less than or equal to 25
System.out.println(numbers.headSet(30));    // [10, 20] — all elements strictly less than 30

// Removing duplicates from a List using a Set — a very common idiom
List<String> withDuplicates = List.of("a", "b", "a", "c", "b");
Set<String> unique = new LinkedHashSet<>(withDuplicates); // preserves first-seen order while deduplicating
System.out.println(unique); // [a, b, c]
```

---

## 🚀 Real World Applications

- `HashSet` for fast membership testing and deduplication where order genuinely doesn't matter (checking whether a username is already taken, deduplicating a large dataset)
- `LinkedHashSet` when deduplicating a list while preserving the original order the items were first encountered — a very common, practical idiom
- `TreeSet` when a sorted, deduplicated collection is needed, or when range queries ("all values between X and Y") matter
- Using a `Set` generally as the idiomatic way to answer "is this collection free of duplicates" or "does this collection contain this element" efficiently

---

## ⚖️ Advantages

Same as the corresponding Map: **HashSet** offers the fastest average membership testing; **LinkedHashSet** adds predictable insertion-order iteration at low cost; **TreeSet** offers guaranteed sorted order and powerful range queries at the cost of `O(log n)` operations instead of `O(1)`.

---

## ⚠️ Limitations

- All the same limitations that apply to the corresponding Map implementation apply here — `HashSet`'s unpredictable order, `TreeSet`'s comparability requirement and slower asymptotic performance, `LinkedHashSet`'s small overhead over plain `HashSet`.
- A broken `equals()`/`hashCode()` on elements stored in a `HashSet`/`LinkedHashSet` can allow genuine duplicates to slip through undetected, or make a present element appear missing — see [[Object Equality - equals(), ==, and hashCode()]] for why this happens.
- `Set` provides no indexed access at all (no `get(i)`) — converting to a `List` or using an iterator is required if positional access is ever needed.

---

## 🚨 Common Mistakes

- Choosing `HashSet` when the actual goal was "deduplicate while preserving original order," when `LinkedHashSet` does exactly that with almost no extra cost.
- Storing custom objects in a `HashSet` without a correct `equals()`/`hashCode()` override, allowing what should be duplicate entries to silently coexist.
- Assuming a `TreeSet` will accept any object type without checking that it implements `Comparable` or that a `Comparator` was supplied — this fails at runtime, not compile time, with a `ClassCastException`.
- Forgetting `Set` has no indexed access — trying to call something like `.get(0)` on a `Set` doesn't compile, since the interface simply doesn't define it.

---

## 📖 Further Reading

- Oracle documentation: `java.util.HashSet`, `java.util.TreeSet`, `java.util.LinkedHashSet` class references
- Oracle documentation: `java.util.NavigableSet` (the interface providing `TreeSet`'s range/navigation methods)

---

## 💡 Wisdom from Mímir

Whenever you reach for a `Set` specifically to deduplicate a `List` while keeping the original order intact — a genuinely common, practical need — `LinkedHashSet` is almost always the right answer, and it's easy to forget it exists in favor of the more familiar `HashSet`.

---

## 🔗 Related Notes

- [[The Collections Framework Overview]]
- [[HashMap vs TreeMap vs LinkedHashMap]]
- [[Object Equality - equals(), ==, and hashCode()]]
- [[Java Codex]]
