---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Python
difficulty: Intermediate
tags:
  - python-idioms
  - context-managers
  - resource-management
aliases:
  - with Statement Python
  - "__enter__ and __exit__"
publish: true
permalink: python/context-managers-and-the-with-statement
---

# <span class="rune">ᛟ</span> Context Managers and the with Statement

> *`with` is Python's answer to Java's try-with-resources — same underlying goal (guaranteed cleanup), and it arrived in Python years earlier, built on nothing more than two dunder methods any class can implement.*

---

## 🎯 Purpose

The `with` statement guarantees that setup and cleanup code run around a block, **even if an exception occurs inside it** — the direct conceptual equivalent of Java's try-with-resources. Any object implementing `__enter__` and `__exit__` (a "context manager") can be used with `with`, and the standard library's `contextlib` module provides a decorator-based shortcut for writing one without a full class.

---

## 🧠 Key Ideas

- `with expression as variable:` calls `expression.__enter__()` (binding its return value to `variable`) before the block runs, and guarantees `expression.__exit__(...)` runs afterward — regardless of whether the block completed normally or raised an exception.
- The built-in pattern `with open("file.txt") as f:` is the single most common real-world example — it guarantees the file is closed afterward, even if an exception occurs while reading it, without needing an explicit `try`/`finally`.
- `__exit__(self, exc_type, exc_value, traceback)` receives details about any exception that occurred inside the block — if it returns `True`, the exception is considered "handled" and suppressed; returning `False` (or `None`, the default) lets it propagate normally.
- `@contextlib.contextmanager` lets you write a context manager as a single generator function instead of a full class with `__enter__`/`__exit__` — code before the `yield` is the setup, code after is the cleanup.
- Multiple context managers can be combined in a single `with` statement (`with open("a") as a, open("b") as b:`), each properly entered and exited in the correct order.

---

## ⚙️ How It Works

`with obj:` calls `obj.__enter__()` first, running whatever setup logic that method contains — then, no matter how the indented block finishes (normally, via `return`, or via an uncaught exception), Python guarantees `obj.__exit__(...)` runs before control leaves the `with` block entirely. This mirrors exactly the guarantee Java's try-with-resources makes for `AutoCloseable` resources — the cleanup code (`__exit__`, or `close()` in Java's case) is never skipped, regardless of how the protected block actually ends.

```text
with open("file.txt") as f:
    data = f.read()
    # even if this line raised an exception, f.close() STILL runs afterward

# roughly equivalent, manually, to:
f = open("file.txt")
try:
    data = f.read()
finally:
    f.close()      # ALWAYS runs — the with statement automates exactly this pattern
```

---

## 💻 Examples

```python
# The most common real-world example
with open("data.txt", "r") as f:
    contents = f.read()
# f is GUARANTEED closed here, even if .read() raised an exception

# Writing a custom context manager as a class
class Timer:
    def __enter__(self):
        import time
        self.start = time.perf_counter()
        return self               # whatever is returned here becomes the "as" variable

    def __exit__(self, exc_type, exc_value, traceback):
        import time
        elapsed = time.perf_counter() - self.start
        print(f"Elapsed: {elapsed:.4f}s")
        return False               # False (or None) — do NOT suppress any exception that occurred

with Timer() as t:
    sum(range(1_000_000))
# Elapsed: 0.0123s — printed automatically, whether or not the block raised

# The MUCH simpler generator-based approach via contextlib
from contextlib import contextmanager
import time

@contextmanager
def timer():
    start = time.perf_counter()
    yield                          # code BEFORE yield = setup, code AFTER yield = cleanup
    elapsed = time.perf_counter() - start
    print(f"Elapsed: {elapsed:.4f}s")

with timer():
    sum(range(1_000_000))

# Multiple context managers in one statement
with open("input.txt") as infile, open("output.txt", "w") as outfile:
    outfile.write(infile.read())

# Suppressing a specific exception via __exit__'s return value
class IgnoreZeroDivision:
    def __enter__(self): return self
    def __exit__(self, exc_type, exc_value, traceback):
        return exc_type is ZeroDivisionError   # True HERE suppresses ONLY this exception type

with IgnoreZeroDivision():
    result = 1 / 0    # normally raises — but __exit__ suppresses it, execution continues after the block
print("Still running!")
```

---

## 🚀 Real World Applications

- Guaranteed file handle cleanup (`with open(...) as f:`) — the single most common use in everyday Python code
- Managing database connections, network sockets, and locks that must always be released, even when an error occurs mid-operation
- Building custom setup/teardown logic for tests, timing blocks, or temporary state changes (e.g., temporarily changing a working directory or suppressing warnings)
- Using `contextlib.contextmanager` to write a context manager quickly, without the boilerplate of a full class with `__enter__`/`__exit__`

---

## ⚖️ Advantages

- Guarantees cleanup code runs regardless of how the protected block exits, eliminating an entire category of "forgot to close/release this" resource leaks.
- The generator-based `@contextmanager` approach is dramatically less boilerplate than writing a full class for simple setup/teardown logic.
- Reads clearly and explicitly at the call site — `with resource:` immediately signals "this resource's lifecycle is being managed here."

---

## ⚠️ Limitations

- Writing a full class-based context manager (with explicit `__enter__`/`__exit__`) is more verbose than the generator-based `@contextmanager` shortcut for simple cases.
- `__exit__`'s ability to suppress exceptions by returning `True` is powerful but can also silently hide real bugs if used carelessly — swallowing an unrelated exception type by accident is a genuine risk.
- Combining many context managers in a single `with` statement can become visually dense and harder to read past a handful of resources.

---

## 🚨 Common Mistakes

- Forgetting `__exit__` must return `True` specifically to suppress an exception — returning `None` (the implicit default if nothing is explicitly returned) lets the exception propagate normally, which is usually the desired behavior and easy to get backwards by mistake.
- Manually opening and closing a file with `f = open(...)` / `f.close()` instead of using `with open(...) as f:`, risking a leaked file handle if an exception occurs between the open and the close.
- Writing an `@contextmanager`-decorated generator function with more than one `yield`, which raises a `RuntimeError` — exactly one `yield` is required, separating setup from cleanup.
- Suppressing exceptions too broadly in a custom `__exit__` (e.g., returning `True` unconditionally) instead of checking `exc_type` and only suppressing the specific exception types genuinely intended to be handled.

---

## 📖 Further Reading

- Python documentation: "The Python Tutorial" — Section 8.6, "Predefined Clean-up Actions"
- Python documentation: `contextlib` — Utilities for `with`-statement contexts

---

## 💡 Wisdom from Mímir

Any time you're about to write a manual `try`/`finally` purely to guarantee some cleanup step runs, stop and ask whether a context manager — even a quick one via `@contextmanager` — would express the same guarantee more clearly. `with` isn't just shorter; it signals the resource-lifecycle intent directly at the call site, the way a bare `try`/`finally` never quite does.

---

## 🔗 Related Notes

- [[Decorators]]
- [[Exception Handling - try, except, else, and finally]]
- [[Iterators and Generators]]
- [[Python Codex]]
