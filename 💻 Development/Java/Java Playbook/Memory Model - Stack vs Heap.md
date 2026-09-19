---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Java
difficulty: Intermediate
tags:
  - jvm
  - memory-model
  - stackoverflowerror
aliases:
  - Java Stack and Heap
  - StackOverflowError vs OutOfMemoryError
publish: true
permalink: java/memory-model-stack-vs-heap
---

# <span class="rune">ᛟ</span> Memory Model - Stack vs Heap

> *Every method call gets its own small, temporary slab of memory that vanishes the instant that call returns. Every object gets a slab that lingers until nothing anywhere still points to it. Confusing which kind of memory something lives in is where "why does this variable still have its old value" bugs come from.*

---

## 🎯 Purpose

The JVM divides runtime memory into two conceptually distinct regions: the **stack**, holding method call frames and local variables (including primitives, and references to objects), and the **heap**, holding the actual objects those references point to. This split directly explains [[Primitive Types vs Reference Types|the primitive-vs-reference distinction]] at a physical level, and it's the reason two very different kinds of runtime errors — `StackOverflowError` and `OutOfMemoryError` — exist for two entirely different failure modes.

---

## 🧠 Key Ideas

- Each thread has its **own stack** — a stack of "frames," one pushed for every method call currently in progress, popped off when that method returns.
- A stack frame holds that method's **local variables** (primitives directly, object references as pointers) and is automatically reclaimed the instant the method returns — no garbage collection involved for stack memory at all.
- The **heap** is a single shared region (across all threads) where every object created with `new` actually lives — heap memory isn't automatically reclaimed when a method returns; it persists until the garbage collector determines nothing references it anymore (see [[Garbage Collection Basics]]).
- `StackOverflowError` occurs when the call stack grows too deep — classically from unbounded or incorrect recursion with no proper base case.
- `OutOfMemoryError` occurs when the heap fills up faster than the garbage collector can reclaim unreachable objects — often from genuine memory leaks (references unintentionally kept alive) or simply trying to hold more live data than the configured heap size allows.

---

## ⚙️ How It Works

Calling a method pushes a new frame onto the current thread's stack, sized to hold that method's local variables and any parameters — when the method returns (normally or via an exception), its entire frame is popped and instantly reclaimed, which is why local variables are only ever visible for the duration of that specific call. Objects, by contrast, are always allocated on the shared heap regardless of which method or thread created them — a reference *to* that object might live briefly on the stack (a local variable), but the object itself persists on the heap independently, for as long as *any* reachable reference anywhere still points to it, even after the method that originally created it has long since returned.

```text
void methodA() {
    int x = 5;              // x lives on methodA's STACK FRAME
    Dog d = new Dog("Rex"); // "d" (the reference) lives on the STACK; the actual Dog OBJECT lives on the HEAP
    methodB();
}   // methodA's frame is POPPED here — x and the reference "d" are gone,
    // but the actual Dog object on the heap MAY still be alive if something else still references it
```

---

## 💻 Examples

```java
public class MemoryDemo {
    public static void main(String[] args) {
        int localPrimitive = 42;              // stack: main()'s frame
        Dog myDog = new Dog("Rex");            // stack: reference "myDog"; heap: the actual Dog object
        processDog(myDog);
    }                                           // main()'s frame popped here

    static void processDog(Dog dog) {           // NEW stack frame for processDog()
        System.out.println(dog.getName());       // "dog" is a SEPARATE reference, same heap object
    }                                             // this frame popped when processDog() returns —
                                                    // the Dog object on the heap is UNAFFECTED
}

// StackOverflowError — unbounded recursion, stack grows until it runs out of space
void recurse() {
    recurse(); // no base case — keeps pushing new frames until StackOverflowError
}

// OutOfMemoryError — heap fills up faster than it can be reclaimed
List<byte[]> leak = new ArrayList<>();
while (true) {
    leak.add(new byte[1_000_000]); // keeps adding to a list that's NEVER cleared —
                                     // every array stays reachable, heap eventually fills up
}
```

---

## 🚀 Real World Applications

- Diagnosing a `StackOverflowError` by checking recursive method calls for a missing or incorrect base case
- Diagnosing an `OutOfMemoryError` by looking for collections or caches that grow unbounded and are never cleared, keeping objects reachable (and therefore uncollectable) indefinitely
- Understanding why a local variable's value "disappears" the moment its enclosing method returns, while the object it referenced might still be alive elsewhere
- Reasoning about the performance tradeoff between stack allocation (cheap, fast, automatic) and heap allocation (more expensive, garbage-collected) when designing performance-sensitive code

---

## ⚖️ Advantages

- Stack allocation/deallocation is extremely fast and fully automatic — no garbage collector involvement needed for local variables at all.
- The heap's shared, long-lived nature is exactly what allows objects to outlive the specific method call that created them and be passed around freely between methods and threads.
- Separating these two regions gives the JVM clear, distinct strategies for managing two very different memory lifetimes (short-lived call-scoped data vs. potentially long-lived shared objects).

---

## ⚠️ Limitations

- Stack size is limited (and configurable, but still finite) — deep recursion, even when logically correct, can exhaust it in ways that a purely iterative equivalent wouldn't.
- Heap memory is not automatically reclaimed the instant an object becomes unreachable — garbage collection runs periodically, which introduces both a small delay before memory is actually freed and occasional GC pause overhead (see [[Garbage Collection Basics]]).
- Diagnosing heap-related memory leaks (objects unintentionally kept reachable) is often significantly harder than diagnosing a stack overflow, since the leak is usually a subtle reference held somewhere unexpected rather than an obvious recursive call.

---

## 🚨 Common Mistakes

- Writing a recursive method without a correct, reachable base case, causing `StackOverflowError` on sufficiently large or malformed input.
- Assuming an object is automatically "freed" the instant the method that created it returns — the object persists on the heap as long as *any* reference to it remains reachable, regardless of which method created it.
- Adding to a static collection (or any long-lived cache) without ever removing old entries, gradually accumulating unreachable-in-spirit-but-still-referenced objects until `OutOfMemoryError` eventually occurs.
- Confusing "the reference variable went out of scope" with "the object was garbage collected" — these are related but distinct events, and the object's actual collection timing is controlled by the garbage collector, not by the reference going out of scope.

---

## 📖 Further Reading

- Oracle: "The Java Virtual Machine Specification" (Chapter 2, Runtime Data Areas)
- Oracle documentation: `java.lang.StackOverflowError`, `java.lang.OutOfMemoryError`

---

## 💡 Wisdom from Mímir

When a bug looks like "this object should have been cleaned up but wasn't," resist the instinct to blame the garbage collector — the far more common cause is a forgotten reference somewhere (a static collection, a listener never removed, a cache never evicted) still quietly keeping that object reachable. The GC is doing exactly its job; the question is almost always why the object still *looks* reachable to it.

---

## 🔗 Related Notes

- [[Primitive Types vs Reference Types]]
- [[Garbage Collection Basics]]
- [[How the JVM Works - Compilation, Bytecode, and Class Loading]]
- [[Java Codex]]
