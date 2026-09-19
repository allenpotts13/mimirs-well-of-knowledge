---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Java
difficulty: Intermediate
tags:
  - exceptions
  - checked-exceptions
  - runtimeexception
aliases:
  - RuntimeException vs Exception
  - throws Clause
publish: true
permalink: java/checked-vs-unchecked-exceptions
---

# <span class="rune">ᛟ</span> Checked vs Unchecked Exceptions

> *Java's exception hierarchy makes a promise no other mainstream language quite makes the same way: some failures are so foreseeable the compiler simply won't let you forget to plan for them.*

---

## 🎯 Purpose

Java splits exceptions into two categories with fundamentally different compiler treatment: **checked exceptions**, which the compiler *forces* calling code to either catch or explicitly declare (via `throws`), and **unchecked exceptions**, which require no such declaration at all. This distinction is unique enough to Java (compared to most other mainstream languages) that it's a frequent point of both design debate and confusion for newcomers.

---

## 🧠 Key Ideas

- **Checked exceptions** extend `Exception` (but not `RuntimeException`) — the compiler requires every method that might throw one to either catch it or declare it with `throws`, or the code simply won't compile. `IOException` and `SQLException` are classic examples.
- **Unchecked exceptions** extend `RuntimeException` (which itself extends `Exception`) — the compiler enforces nothing; they can be thrown and propagate freely without any `throws` declaration. `NullPointerException`, `ArrayIndexOutOfBoundsException`, and `IllegalArgumentException` are common examples.
- `Error` (a third category, sibling to `Exception` under `Throwable`) represents serious problems generally not meant to be caught at all — `OutOfMemoryError`, `StackOverflowError` — signaling conditions an application typically can't meaningfully recover from.
- Checked exceptions are meant for **recoverable, foreseeable** conditions the caller should be forced to consider (a file that might not exist, a network call that might fail); unchecked exceptions typically represent **programming errors** (a null reference, an invalid argument, an out-of-bounds index) that ideally shouldn't happen if the code is correct.
- The full hierarchy: `Throwable` → `Exception` (checked, unless a `RuntimeException` subtype) and `Error` (both unchecked, but for very different reasons).

---

## ⚙️ How It Works

The compiler tracks, for every method, which checked exceptions it might throw — either directly (via a `throw` statement) or transitively (by calling another method that itself declares a checked exception). Any caller of that method must handle this at compile time: either wrap the call in a `try/catch` for that exception type, or declare the same exception in its own `throws` clause, pushing the responsibility one level further up the call chain. Unchecked exceptions bypass this entire compile-time tracking system — they can be thrown from anywhere and will simply propagate up the call stack at runtime until *something* catches them, or the program terminates if nothing does.

```text
void readFile() throws IOException {   // checked — MUST be declared or caught
    FileReader fr = new FileReader("data.txt"); // can throw IOException
}

void divide(int a, int b) {             // no throws clause needed — unchecked
    int result = a / b;                  // CAN throw ArithmeticException, but compiler doesn't require handling it
}
```

---

## 💻 Examples

```java
import java.io.FileReader;
import java.io.IOException;

// Checked exception — MUST be handled or declared
void readFile(String path) throws IOException {  // declares it — pushes responsibility to the caller
    FileReader reader = new FileReader(path);
}

void caller() {
    try {
        readFile("data.txt"); // must catch it here, since readFile() declared it
    } catch (IOException e) {
        System.out.println("Could not read file: " + e.getMessage());
    }
}

// Unchecked exception — no declaration required, propagates freely
void divide(int a, int b) {
    System.out.println(a / b); // can throw ArithmeticException — compiler doesn't require handling
}

void anotherCaller() {
    divide(10, 0); // compiles fine; throws ArithmeticException at RUNTIME if actually called this way
}

// Custom checked exception
class InsufficientFundsException extends Exception {   // extends Exception, NOT RuntimeException — checked
    public InsufficientFundsException(String message) { super(message); }
}

// Custom unchecked exception
class InvalidAgeException extends RuntimeException {   // extends RuntimeException — unchecked
    public InvalidAgeException(String message) { super(message); }
}
```

---

## 🚀 Real World Applications

- Using checked exceptions for genuinely recoverable I/O, network, and database operations where the caller should be forced to consider the failure case (`IOException`, `SQLException`)
- Using unchecked exceptions for programming errors and invalid input that indicate a bug rather than an expected, recoverable condition (`IllegalArgumentException`, `IllegalStateException`)
- Designing a custom exception hierarchy for an application's domain-specific error conditions, choosing checked vs. unchecked deliberately based on whether callers should be compiler-forced to handle it
- Understanding why some library methods force a `try/catch` (or a `throws` declaration) while others compile without any such requirement at all

---

## ⚖️ Advantages

- Checked exceptions make foreseeable failure modes visible directly in a method's signature, documenting what can go wrong as part of the compiled contract itself.
- The compiler-enforced handling of checked exceptions catches an entire category of "forgot to handle this failure" bugs before the program ever runs.
- Unchecked exceptions avoid cluttering every method signature up the call chain with `throws` declarations for conditions that genuinely represent bugs rather than expected, recoverable failures.

---

## ⚠️ Limitations

- Checked exceptions are widely criticized (including by parts of the Java standard library's own evolving design) for encouraging poor error-handling patterns — an empty `catch` block that silently swallows an exception just to satisfy the compiler is a very common anti-pattern.
- Overusing checked exceptions in a class hierarchy that goes through many layers of method calls forces every intermediate method to add `throws` declarations, even when it has no meaningful way to handle the exception itself.
- The distinction between "should be checked" and "should be unchecked" is ultimately a design judgment call, and reasonable developers frequently disagree about where a specific exception type belongs.

---

## 🚨 Common Mistakes

- Catching a checked exception just to satisfy the compiler and then doing nothing meaningful with it (an empty `catch` block), silently hiding a real failure instead of actually handling it.
- Declaring `throws Exception` broadly on a method instead of the specific checked exception types it can actually throw, forcing callers to handle an overly generic type and losing useful information about what can actually go wrong.
- Assuming all `RuntimeException`s are always bugs that should never be caught — sometimes catching a specific unchecked exception (like `NumberFormatException` when parsing untrusted user input) is entirely legitimate and expected.
- Creating a custom exception and forgetting to choose `Exception` (checked) vs. `RuntimeException` (unchecked) deliberately, defaulting to whichever one happened to compile first without thinking through whether callers should be compiler-forced to handle it.

---

## 📖 Further Reading

- Oracle Java Tutorials: "Unchecked Exceptions — The Controversy"
- Oracle Java Tutorials: "The Catch or Specify Requirement"

---

## 💡 Wisdom from Mímir

Before catching any checked exception, ask honestly: can this specific piece of code actually *do* something meaningful in response, or am I just catching it to make the compiler stop complaining? If it's the latter, the exception should probably be declared with `throws` and handled further up the call chain, where there's actually enough context to respond to it properly.

---

## 🔗 Related Notes

- [[Try-Catch-Finally and Try-With-Resources]]
- [[Creating Custom Exceptions]]
- [[Java Codex]]
