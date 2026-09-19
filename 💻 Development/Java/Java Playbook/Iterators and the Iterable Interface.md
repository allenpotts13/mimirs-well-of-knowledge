---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Java
difficulty: Intermediate
tags:
  - collections
  - iterators
  - concurrentmodificationexception
aliases:
  - Iterator.remove()
  - ConcurrentModificationException
publish: true
permalink: java/iterators-and-the-iterable-interface
---

# <span class="rune">ᛟ</span> Iterators and the Iterable Interface

> *The enhanced for-each loop you use every day is just an Iterator wearing a friendlier syntax — and the moment you need to remove something mid-loop, that disguise comes off, because only the Iterator itself knows how to do it safely.*

---

## 🎯 Purpose

The `Iterable<T>` interface (which every `Collection` implements) is what makes the enhanced for-each loop (`for (T item : collection)`) possible — it's compiler sugar for obtaining an `Iterator<T>` and calling its methods in a loop. Understanding the real `Iterator` underneath matters the moment you need to safely remove elements *while* iterating, something the for-each loop cannot do at all.

---

## 🧠 Key Ideas

- `Iterable<T>` declares one method, `iterator()`, which returns an `Iterator<T>` — any class implementing `Iterable` can be used in a for-each loop.
- `Iterator<T>` provides three core methods: `hasNext()` (is there another element), `next()` (return the next element and advance), and `remove()` (safely remove the last element returned by `next()`).
- The enhanced for-each loop is syntactic sugar — `for (String s : list)` compiles down to essentially the same code as manually calling `list.iterator()` and looping with `hasNext()`/`next()`.
- Modifying a collection's structure (adding or removing elements) **while** iterating it with a for-each loop throws `ConcurrentModificationException` — the only safe way to remove elements during iteration is through the `Iterator`'s own `remove()` method.
- `ListIterator<T>` (available only on `List` implementations) extends `Iterator` further, adding backward traversal and the ability to `set()` or `add()` elements during iteration.

---

## ⚙️ How It Works

Collections maintain an internal `modCount` (modification count) that increments every time the collection's structure changes. An `Iterator` created from that collection captures the `modCount` at creation time, and every call to `next()` checks whether the collection's current `modCount` still matches what the iterator expects — if something modified the collection through any path *other than that same iterator's own `remove()` method*, the mismatch is detected and `ConcurrentModificationException` is thrown immediately, specifically to prevent silent, undefined behavior from iterating over a structure that changed underneath it.

```text
for (String s : list) {
    if (s.equals("x")) list.remove(s); // modifies list DIRECTLY — bypasses the iterator's own bookkeeping
}
// throws ConcurrentModificationException on the NEXT call to next()

Iterator<String> it = list.iterator();
while (it.hasNext()) {
    if (it.next().equals("x")) it.remove(); // safe — goes through the SAME iterator, modCount stays in sync
}
```

---

## 💻 Examples

```java
List<String> names = new ArrayList<>(List.of("Alice", "Bob", "Charlie", "Bob"));

// Enhanced for-each — fine for READ-ONLY iteration
for (String name : names) {
    System.out.println(name);
}

// UNSAFE — throws ConcurrentModificationException
// for (String name : names) {
//     if (name.equals("Bob")) names.remove(name);
// }

// SAFE — removal via the Iterator's own remove() method
Iterator<String> it = names.iterator();
while (it.hasNext()) {
    String name = it.next();
    if (name.equals("Bob")) {
        it.remove();   // safely removes the CURRENT element
    }
}
System.out.println(names); // [Alice, Charlie]

// ListIterator — bidirectional, supports set() and add() mid-iteration
ListIterator<String> listIt = names.listIterator();
while (listIt.hasNext()) {
    String name = listIt.next();
    if (name.equals("Alice")) {
        listIt.set("ALICE"); // replaces the current element
    }
}

// A custom Iterable
class Range implements Iterable<Integer> {
    private final int start, end;
    Range(int start, int end) { this.start = start; this.end = end; }

    @Override
    public Iterator<Integer> iterator() {
        return new Iterator<>() {
            int current = start;
            public boolean hasNext() { return current < end; }
            public Integer next() { return current++; }
        };
    }
}

for (int i : new Range(1, 5)) {
    System.out.println(i); // 1, 2, 3, 4
}
```

---

## 🚀 Real World Applications

- Safely removing elements matching a condition from a `List`/`Set` while iterating, without triggering `ConcurrentModificationException`
- Implementing `Iterable` on a custom data structure (a linked list, a tree, a graph) to make it usable directly in for-each loops
- Using `ListIterator` to traverse a `List` backward, or to replace elements in place during a single pass
- Understanding why a seemingly-reasonable `for (x : list) { list.remove(x); }` pattern fails, and knowing the correct fix

---

## ⚖️ Advantages

- `ConcurrentModificationException` catches a genuinely dangerous class of bug (modifying a structure while traversing it) fail-fast and loudly, rather than allowing silent, unpredictable corruption.
- The enhanced for-each loop provides clean, concise syntax for the overwhelmingly common case (read-only iteration), while the full `Iterator` remains available for the less common but necessary mutation case.
- Implementing `Iterable` on a custom class integrates it seamlessly with for-each loops and the rest of the language's iteration idioms.

---

## ⚠️ Limitations

- `ConcurrentModificationException` detection is "best-effort," not a strict guarantee — the JVM specification explicitly notes it should not be relied upon for correctness in concurrent contexts; genuinely concurrent collections need different tools entirely (like those in `java.util.concurrent`).
- Using an explicit `Iterator` for removal is noticeably more verbose than a for-each loop, which is a real (if necessary) tradeoff for the safety it provides.
- A custom `Iterator` implementation is easy to get subtly wrong (off-by-one in `hasNext()`, forgetting to advance state in `next()`) if not carefully tested.

---

## 🚨 Common Mistakes

- Removing an element from a collection directly by reference inside a for-each loop, triggering `ConcurrentModificationException` on the very next iteration.
- Using `removeIf()` (a modern, often cleaner alternative) unnecessarily replaced by manual iterator boilerplate, when a simple predicate-based removal would express the same intent more concisely: `names.removeIf(name -> name.equals("Bob"));`.
- Forgetting `Iterator.remove()` removes the *last element returned by `next()`* — calling it before ever calling `next()`, or calling it twice in a row without an intervening `next()`, throws `IllegalStateException`.
- Assuming a for-each loop's underlying iterator is safe to mutate through some *other* reference to the same collection during the same loop, even if not through the collection variable used directly in the loop header.

---

## 📖 Further Reading

- Oracle Java Tutorials: "The Iterator Interface"
- Oracle documentation: `java.util.ConcurrentModificationException`
- Oracle documentation: `Collection.removeIf()`

---

## 💡 Wisdom from Mímir

The instant you need to remove something while looping over a collection, stop reaching for the for-each loop — it's the wrong tool for that specific job, not a bug in Java. Either grab the `Iterator` directly and call its own `remove()`, or reach for `removeIf()` if a simple predicate expresses the condition cleanly.

---

## 🔗 Related Notes

- [[The Collections Framework Overview]]
- [[ArrayList vs LinkedList]]
- [[Control Flow - if, switch, and Loops]]
- [[Java Codex]]
