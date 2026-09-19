---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Java
difficulty: Advanced
tags:
  - concurrency
  - synchronization
  - race-conditions
aliases:
  - synchronized Keyword
  - Thread Safety
publish: true
permalink: java/synchronization-and-race-conditions
---

# <span class="rune">ᛟ</span> Synchronization and Race Conditions

> *`count++` looks like one operation. It is actually three — read, increment, write — and the entire concept of a race condition exists because two threads can each execute the first step before either finishes the third.*

---

## 🎯 Purpose

A **race condition** occurs when two or more threads access shared mutable state concurrently, and the final outcome depends on the unpredictable timing of their execution — producing incorrect results that may not even be consistently reproducible. The `synchronized` keyword (and related tools) provide **mutual exclusion**, ensuring only one thread at a time can execute a critical section of code, eliminating the specific class of bug that race conditions cause.

---

## 🧠 Key Ideas

- Even a seemingly simple, single-line operation like `count++` is actually three separate steps at the machine level — read the current value, add one, write the new value back — and if two threads interleave those steps, an increment can be silently lost.
- `synchronized` (as a method modifier, or a block wrapping specific code) ensures only **one thread at a time** can execute that method/block on a given object, blocking any other thread attempting to enter until the first one finishes.
- Every object in Java has an intrinsic **monitor lock** — `synchronized` methods/blocks acquire that lock automatically on entry and release it automatically on exit, even if an exception is thrown partway through.
- **Deadlock** occurs when two or more threads each hold a lock the other needs, and neither can proceed — a genuinely serious hazard specifically introduced by using multiple locks, generally avoided by consistently acquiring locks in the same order everywhere.
- The `java.util.concurrent.atomic` package (`AtomicInteger`, `AtomicLong`, etc.) provides lock-free thread-safe operations for simple cases like counters, often a simpler and more efficient alternative to `synchronized` for that specific narrow use case.

---

## ⚙️ How It Works

When multiple threads share a mutable variable without any coordination, the JVM makes no guarantee about how their individual read/modify/write steps interleave — two threads both reading `count` as `5` before either writes back `6` means one entire increment is silently lost, with no exception, no warning, just a quietly wrong final value. `synchronized` prevents this specific interleaving by ensuring that once one thread has acquired the relevant lock and entered the critical section, every other thread attempting to enter the *same* synchronized method/block (on the *same* object) must wait until the first thread has completely finished and released the lock — guaranteeing the read-modify-write sequence for one thread fully completes before another thread's sequence can begin.

```text
Thread A: read count (5)
Thread B: read count (5)          ← BOTH read the SAME stale value
Thread A: write count = 6
Thread B: write count = 6          ← one increment is LOST — should have been 7

// With synchronized:
Thread A: [acquires lock] read count (5), write count = 6 [releases lock]
Thread B: [WAITS for lock] ... [acquires lock] read count (6), write count = 7 [releases lock]
// correct — 7, because B's entire sequence only runs AFTER A's is fully complete
```

---

## 💻 Examples

```java
// UNSAFE — a classic race condition
class Counter {
    private int count = 0;
    public void increment() {
        count++;  // NOT atomic — read, add, write are three separate steps
    }
    public int getCount() { return count; }
}

// With multiple threads calling increment() concurrently, the final count is
// often LESS than expected — some increments are silently lost.

// SAFE — synchronized method
class SafeCounter {
    private int count = 0;
    public synchronized void increment() {  // only ONE thread at a time can execute this
        count++;
    }
    public synchronized int getCount() { return count; }
}

// SAFE — synchronized block (finer-grained, locks only what's necessary)
class SafeCounter2 {
    private int count = 0;
    private final Object lock = new Object();

    public void increment() {
        synchronized (lock) {  // only this critical section is protected
            count++;
        }
    }
}

// SAFE — AtomicInteger, often simpler and faster for this specific narrow case
import java.util.concurrent.atomic.AtomicInteger;

class AtomicCounter {
    private final AtomicInteger count = new AtomicInteger(0);
    public void increment() {
        count.incrementAndGet(); // genuinely atomic, no explicit locking needed
    }
    public int getCount() { return count.get(); }
}

// Deadlock example — two threads, two locks, ACQUIRED IN OPPOSITE ORDER
Object lockA = new Object();
Object lockB = new Object();

// Thread 1: synchronized(lockA) { synchronized(lockB) { ... } }
// Thread 2: synchronized(lockB) { synchronized(lockA) { ... } }
// If both threads acquire their first lock at the same time, NEITHER can get the second — DEADLOCK
```

---

## 🚀 Real World Applications

- Protecting shared mutable state (counters, caches, shared collections) accessed by multiple threads in a concurrent application
- Choosing `AtomicInteger`/`AtomicLong` over `synchronized` for simple counter-style shared state, for better performance under contention
- Designing multi-lock code carefully (consistent lock ordering) to avoid deadlock in systems where more than one lock is genuinely needed
- Recognizing intermittent, hard-to-reproduce bugs in multithreaded code as likely race conditions rather than "random" or unexplainable behavior

---

## ⚖️ Advantages

- `synchronized` provides a straightforward, built-in mechanism for mutual exclusion without needing an external concurrency library for basic cases.
- Atomic classes offer better performance than full locking for simple, narrow use cases like counters and flags.
- Correctly applied synchronization eliminates race conditions entirely for the protected state, producing fully deterministic, correct results regardless of thread timing.

---

## ⚠️ Limitations

- `synchronized` introduces genuine performance overhead and contention — threads waiting for a lock are blocked, not doing useful work, which can become a real bottleneck under high concurrency.
- Race conditions are notoriously difficult to reproduce and debug — they often depend on precise timing that varies between runs, machines, and load conditions, meaning a bug might pass every test yet still occur rarely in production.
- Using multiple locks without careful discipline introduces deadlock risk, a failure mode that can freeze an application entirely, rather than merely producing an incorrect result.
- Over-synchronizing (locking far more code than necessary, or locking on the wrong object) can silently fail to actually prevent the intended race condition while still paying the full performance cost.

---

## 🚨 Common Mistakes

- Assuming a simple compound operation like `count++` or `if (x == null) x = new Thing();` is atomic, when it's actually multiple separate steps vulnerable to interleaving from other threads.
- Synchronizing on different objects across different methods/threads that are supposed to be protecting the *same* shared state — locks only provide mutual exclusion when threads actually contend for the *same* lock object.
- Acquiring multiple locks in inconsistent order across different parts of a codebase, creating a deadlock risk that may only manifest rarely, under specific timing conditions.
- Reaching for `synchronized` everywhere reflexively instead of considering whether a higher-level, purpose-built concurrency utility (an atomic class, a concurrent collection from `java.util.concurrent`, or [[The Executor Framework]]) would be a simpler and more appropriate fit.

---

## 📖 Further Reading

- Oracle Java Tutorials: "Synchronization"
- Oracle Java Tutorials: "Concurrency" (the broader `java.util.concurrent` package)

---

## 💡 Wisdom from Mímir

Whenever a multithreaded bug seems to appear "randomly" and can't be reliably reproduced, that inconsistency is itself the diagnostic clue — race conditions depend on timing that varies between runs, and a bug that only shows up occasionally under load is a far stronger signal of a race condition than a consistent, reproducible failure ever would be.

---

## 🔗 Related Notes

- [[Threads and the Runnable Interface]]
- [[The Executor Framework]]
- [[Java Codex]]
