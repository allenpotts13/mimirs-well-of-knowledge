---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Java
difficulty: Intermediate
tags:
  - concurrency
  - threads
  - runnable
aliases:
  - Extending Thread vs Runnable
  - Java Multithreading Basics
publish: true
permalink: java/threads-and-the-runnable-interface
---

# <span class="rune">ᛟ</span> Threads and the Runnable Interface

> *A Thread and a Runnable answer two different questions — "what mechanism runs code concurrently" and "what code should run" — and Java lets you provide the second without committing to a specific answer for the first.*

---

## 🎯 Purpose

A `Thread` represents an independent path of execution within a program, letting multiple sequences of code run concurrently. Java offers two ways to define what a thread should actually do: extending the `Thread` class directly and overriding `run()`, or implementing the `Runnable` functional interface and passing it to a `Thread` — the second approach is broadly preferred, for reasons that become clear once you consider [[Inheritance and the extends Keyword|Java's single-inheritance restriction]].

---

## 🧠 Key Ideas

- `Thread` is a class representing an actual OS-level thread of execution; `Runnable` is a functional interface (one abstract method, `run()`) describing *what code* should execute, independent of the threading mechanism itself.
- Extending `Thread` directly uses up a class's one and only `extends` slot (see [[Inheritance and the extends Keyword]]) — a real limitation if the class already needs to extend something else.
- Implementing `Runnable` instead keeps the class free to extend something else, and — because `Runnable` is a functional interface — it can be supplied as a [[Lambda Expressions|lambda expression]] directly, without a separate class at all.
- Calling `.start()` on a `Thread` actually creates a new OS-level thread and begins executing `run()` concurrently; calling `.run()` directly (a common beginner mistake) just executes the method like any ordinary method call, entirely on the *current* thread, with no concurrency at all.
- `Thread.sleep(millis)` pauses the current thread for approximately the given duration; `.join()` makes the calling thread wait until the target thread finishes before continuing.

---

## ⚙️ How It Works

`new Thread(runnableInstance).start()` asks the JVM to allocate a real OS-level thread and schedule `runnableInstance.run()` to execute on it, running concurrently alongside whatever thread created it (typically the main thread) — from that point, both threads are genuinely running in parallel (or interleaved, depending on CPU core availability), with no defined order between statements running on different threads unless explicit synchronization (see [[Synchronization and Race Conditions]]) is introduced. Calling `runnableInstance.run()` directly, without `.start()`, skips all of that entirely — it's just an ordinary synchronous method call, executing on whatever thread made the call, with zero concurrency involved.

```text
new Thread(myRunnable).start();  // creates a NEW thread, runs myRunnable.run() CONCURRENTLY
myRunnable.run();                 // just an ORDINARY method call — runs on the CURRENT thread, no concurrency
```

---

## 💻 Examples

```java
// Approach 1: extending Thread directly (uses up the one extends slot)
class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("Running in: " + Thread.currentThread().getName());
    }
}
new MyThread().start();

// Approach 2: implementing Runnable (preferred — keeps extends free)
class MyTask implements Runnable {
    @Override
    public void run() {
        System.out.println("Running in: " + Thread.currentThread().getName());
    }
}
new Thread(new MyTask()).start();

// Approach 3: Runnable as a lambda (most concise, since Runnable is a functional interface)
new Thread(() -> {
    System.out.println("Running in: " + Thread.currentThread().getName());
}).start();

// Common mistake — calling run() directly instead of start()
Thread t = new Thread(() -> System.out.println("Thread: " + Thread.currentThread().getName()));
t.run();    // WRONG for concurrency — runs synchronously on the CURRENT thread
t.start();  // CORRECT — actually starts a new thread

// join() — waiting for a thread to finish before continuing
Thread worker = new Thread(() -> {
    try { Thread.sleep(1000); } catch (InterruptedException e) {}
    System.out.println("Worker finished");
});
worker.start();
worker.join();  // main thread WAITS here until worker completes
System.out.println("Main continues after worker is done");
```

---

## 🚀 Real World Applications

- Running background tasks (file I/O, network calls, long computations) concurrently without blocking the main thread of execution
- Preferring `Runnable` (often as a lambda) over extending `Thread` directly, especially when the class already needs to extend something else
- Using `.join()` to coordinate multiple threads, ensuring one completes before dependent work continues
- Understanding the foundation that higher-level concurrency tools — like [[The Executor Framework]] — are built on top of, even though direct `Thread` usage is increasingly uncommon in modern production code

---

## ⚖️ Advantages

- `Runnable` decouples "what code runs" from "how it's executed," keeping classes free to extend something else and allowing the same task to be run in different ways (directly, via an executor, scheduled, etc.).
- Lambda expressions make simple, short-lived `Runnable` tasks extremely concise to write inline.
- Java's built-in threading support requires no external libraries for basic concurrent execution.

---

## ⚠️ Limitations

- Directly managing raw `Thread` objects (creating, starting, tracking, and cleaning them up manually) becomes unwieldy and error-prone at any meaningful scale — this is exactly the problem [[The Executor Framework|the Executor Framework]] exists to solve.
- Threads are relatively expensive OS resources — creating a new one for every small task, rather than reusing a pool, wastes resources unnecessarily.
- Concurrent code introduces an entirely new category of bugs (race conditions, deadlocks) that don't exist in single-threaded code at all, covered in [[Synchronization and Race Conditions]].

---

## 🚨 Common Mistakes

- Calling `.run()` directly instead of `.start()`, expecting concurrent execution but actually just getting an ordinary synchronous method call on the current thread.
- Extending `Thread` directly out of habit when the class might need to extend something else later, unnecessarily using up Java's single-inheritance slot.
- Creating a large, unmanaged number of raw threads for many small tasks instead of using a thread pool via the Executor Framework, leading to excessive resource consumption and scheduling overhead.
- Assuming threads execute in a specific, predictable order relative to each other without any explicit synchronization or coordination (`.join()`, locks, or higher-level concurrency utilities) to actually enforce that order.

---

## 📖 Further Reading

- Oracle Java Tutorials: "Defining and Starting a Thread"
- Oracle documentation: `java.lang.Thread`, `java.lang.Runnable`

---

## 💡 Wisdom from Mímir

Default to `Runnable` (usually as a lambda) over extending `Thread` directly, as a near-automatic habit — it costs nothing in the simple case, and it keeps the door open for the class to extend something else later without a painful refactor. And whenever concurrent execution "isn't happening," check for the classic `.run()` vs `.start()` mix-up before suspecting anything more complicated.

---

## 🔗 Related Notes

- [[Synchronization and Race Conditions]]
- [[The Executor Framework]]
- [[Functional Interfaces]]
- [[Java Codex]]
