---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Java
difficulty: Intermediate
tags:
  - jvm
  - garbage-collection
  - memory-management
aliases:
  - Java GC
  - Reachability
publish: true
permalink: java/garbage-collection-basics
---

# <span class="rune">ᛟ</span> Garbage Collection Basics

> *The garbage collector never asks "is this object still needed" — it only ever asks "can I still reach this object from somewhere I know is alive." Those two questions usually have the same answer, right up until a forgotten reference makes them diverge.*

---

## 🎯 Purpose

Garbage collection (GC) is the JVM's automatic mechanism for reclaiming heap memory occupied by objects no longer reachable from any active part of the program — freeing developers from the manual memory management (explicit allocation and deallocation) required in languages like C or C++. Understanding roughly how it works — and specifically what "reachability" actually means — explains both why Java rarely suffers classic memory-corruption bugs, and why memory leaks are still entirely possible despite having a garbage collector at all.

---

## 🧠 Key Ideas

- The garbage collector identifies objects as eligible for collection based on **reachability**, not usage — an object is eligible the moment nothing traceable from a set of "GC roots" (active thread stacks, static fields, and a few other root categories) still points to it, regardless of whether the program logically still "needs" it.
- Most JVM garbage collectors use a **generational** strategy — new objects are allocated in a "young generation," where most objects die quickly (a very common real-world pattern); objects that survive multiple collection cycles are promoted to an "old generation," collected less frequently since they're statistically more likely to still be needed.
- GC runs **automatically and asynchronously** — a running program cannot generally control exactly when a collection happens (though `System.gc()` exists as a *hint*, not a guarantee, and is generally discouraged in application code).
- A **memory leak** in Java doesn't mean "forgetting to free memory" the way it does in C — it means keeping an object *reachable* (via a forgotten reference somewhere, like a static collection or an un-removed listener) long after it's actually no longer needed, preventing the GC from ever being able to collect it.
- Multiple garbage collector implementations exist (G1, ZGC, Parallel, and others), each with different tradeoffs between pause time, throughput, and memory overhead — the default varies by JVM version, and the choice matters more for latency-sensitive, high-throughput applications than typical coursework-scale programs.

---

## ⚙️ How It Works

The GC periodically traces every reference chain starting from a fixed set of **GC roots** — things guaranteed to be alive, like local variables currently on any thread's stack, and static fields — marking every object reachable from those roots as "still alive." Anything left unmarked after that trace is, by definition, unreachable from any active part of the program and becomes eligible for collection, its heap memory reclaimed and made available for future allocations. This is precisely why the classic "circular reference" problem from manual reference-counting systems doesn't cause a leak in Java — two objects referencing only each other, with no path back to any GC root, are still correctly identified as unreachable together and collected.

```text
GC Roots (active stack frames, static fields, ...)
    ↓ trace every reachable reference
[Object A] → [Object B] → [Object C]     ← all reachable, all kept alive
[Object X] ←→ [Object Y]                  ← reference each other, but NEITHER reachable from a GC root
                                             → BOTH eligible for collection, despite referencing each other
```

---

## 💻 Examples

```java
// An object becomes eligible for collection the moment it's no longer reachable
Dog myDog = new Dog("Rex");   // reachable — "myDog" is a live local variable
myDog = null;                  // the ORIGINAL Dog object is now unreachable — eligible for GC
                                // (assuming nothing ELSE also references it)

// A "memory leak" in Java — the object stays reachable, so it's NEVER collected
class Cache {
    private static final List<byte[]> cachedData = new ArrayList<>(); // STATIC — lives for the program's entire life

    void addToCache(byte[] data) {
        cachedData.add(data); // keeps growing — every entry stays reachable via the static field forever
    }
}

// Removing a reference explicitly makes an object eligible again
Cache cache = new Cache();
cache.addToCache(new byte[1_000_000]);
// If nothing ever removes entries from cachedData, this "leaks" — not because Java forgot to free it,
// but because the static list keeps every single entry reachable indefinitely.

// Circular references DON'T cause a leak in Java (unlike naive reference-counting systems)
class Node {
    Node partner;
}

Node a = new Node();
Node b = new Node();
a.partner = b;
b.partner = a;   // a and b reference EACH OTHER

a = null;
b = null;         // NEITHER local variable references them anymore —
                    // even though a.partner and b.partner still point to each other,
                    // NOTHING reachable from a GC root points to either — both ARE collected
```

---

## 🚀 Real World Applications

- Diagnosing an `OutOfMemoryError` by looking for static collections, caches, or listeners that keep objects reachable long after they're logically no longer needed
- Understanding why explicitly setting a large object's reference to `null` can sometimes help the GC reclaim memory sooner in specific, long-lived scopes
- Choosing an appropriate garbage collector implementation and heap size for latency-sensitive or high-throughput production applications
- Recognizing that Java's automatic memory management eliminates entire categories of bugs common in manually-managed languages (dangling pointers, double-frees, use-after-free), while still requiring awareness of reachability to avoid leaks

---

## ⚖️ Advantages

- Eliminates manual memory management entirely for the overwhelming majority of application code, along with the classic bug categories (dangling pointers, double-free, use-after-free) that come with it.
- Correctly handles circular references without any special developer effort — a genuine advantage over naive manual reference-counting schemes.
- Generational collection strategies are well-tuned for the common real-world pattern where most objects are short-lived, making garbage collection efficient in typical applications without manual tuning.

---

## ⚠️ Limitations

- Garbage collection introduces occasional pause times (though modern collectors like ZGC and G1 minimize this significantly) — a real consideration for latency-sensitive applications.
- Memory leaks are still entirely possible in Java — the GC only prevents *unreachable* memory from persisting; it does nothing to prevent memory that's still (unintentionally) reachable from accumulating indefinitely.
- `System.gc()` is only a hint to the JVM, not a command — calling it doesn't guarantee an immediate (or even eventual) collection, and relying on it in application logic is a well-known anti-pattern.
- The exact timing of collection is intentionally opaque to application code, which can make certain kinds of precise resource-timing behavior harder to reason about compared to deterministic manual memory management.

---

## 🚨 Common Mistakes

- Assuming Java's garbage collector makes memory leaks impossible — it only prevents leaks caused by *lost* references (the classic C-style problem); it does nothing to prevent leaks caused by references that are unintentionally *kept* alive.
- Adding entries to a static collection or cache without any corresponding removal logic, causing exactly this kind of "still reachable, never actually needed again" leak.
- Calling `System.gc()` expecting an immediate, guaranteed collection — it's a hint the JVM is free to ignore, and relying on it in real application logic is considered poor practice.
- Assuming setting a reference to `null` is generally necessary for good memory hygiene — for ordinary local variables that simply go out of scope naturally, it's unnecessary; it matters mainly for long-lived references (fields, static state) that would otherwise remain reachable far longer than needed.

---

## 📖 Further Reading

- Oracle: "Garbage Collection Tuning Guide"
- Oracle documentation: `java.lang.Runtime.gc()` (and why it's discouraged for application use)

---

## 💡 Wisdom from Mímir

Whenever an `OutOfMemoryError` shows up, resist the instinct to blame the garbage collector for "not doing its job" — it's almost always doing exactly what it's designed to do: correctly refusing to collect something that's still, technically, reachable. The real question is always "what's still holding a reference to this that shouldn't be," and that question usually leads straight to a static collection, a cache, or a listener nobody remembered to clean up.

---

## 🔗 Related Notes

- [[Memory Model - Stack vs Heap]]
- [[How the JVM Works - Compilation, Bytecode, and Class Loading]]
- [[Java Codex]]
