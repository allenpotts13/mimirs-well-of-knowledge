---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Python
difficulty: Intermediate
tags:
  - error-handling
  - assert
  - defensive-programming
aliases:
  - assert Statement Python
  - python -O flag
publish: true
permalink: python/assert-and-defensive-programming
---

# <span class="rune">ᛟ</span> assert and Defensive Programming

> *Run Python with the `-O` flag, and every `assert` statement in the entire program simply vanishes — not just skipped, genuinely removed before execution. Anything an assert was supposed to guarantee is now nobody's job at all.*

---

## 🎯 Purpose

`assert` is a debugging and internal-invariant-checking tool — it verifies a condition that *should* always be true if the code is functioning correctly, raising `AssertionError` immediately if it isn't. Critically, `assert` statements can be **globally stripped out** when Python runs in optimized mode, which makes them fundamentally unsuitable for anything a program's actual correctness or security depends on.

---

## 🧠 Key Ideas

- `assert condition, "optional message"` raises `AssertionError` with that message if `condition` is falsy — otherwise, it does nothing at all and execution continues normally.
- Running Python with the `-O` (optimize) flag **strips every `assert` statement from the compiled bytecode entirely** — they don't just silently pass, they're removed as if never written, meaning any side effects inside the asserted expression also vanish.
- `assert` is intended for catching **programming errors** and verifying internal assumptions during development/testing — never for validating external input, user input, or anything a program's actual runtime behavior or security genuinely depends on.
- Because assertions can be globally disabled, input validation and business-rule enforcement should always use regular `if`/`raise` statements (typically raising `ValueError` or a [[Custom Exceptions in Python|custom exception]]) instead of `assert`.
- A common, genuinely dangerous mistake is using `assert` to check user-supplied data or security conditions — code that "works" in normal testing can silently skip all of its validation entirely when deployed with `-O`.

---

## ⚙️ How It Works

`assert condition, message` compiles into bytecode that checks `condition` and raises `AssertionError(message)` if it's falsy — but this check is wrapped by the compiler in a way specifically tied to Python's optimization level: running with `-O` (or setting `PYTHONOPTIMIZE`) causes the compiler to skip generating that bytecode at all, as if the `assert` line were never written in the source file in the first place. This is precisely why `assert` is documented as unsuitable for anything load-bearing — the moment a deployment happens to run with optimization enabled, every single assertion in the codebase disappears simultaneously, with no warning.

```text
assert user.is_authenticated, "User must be logged in"   # NEVER do this for real security checks —
                                                              # running with -O REMOVES this check entirely,
                                                              # silently granting access to anyone
```

---

## 💻 Examples

```python
# Appropriate use — verifying an internal invariant during development
def calculate_average(numbers):
    assert len(numbers) > 0, "Cannot calculate average of an empty list"
    return sum(numbers) / len(numbers)

# Appropriate use — a sanity check on a function's OWN internal logic
def binary_search(sorted_list, target):
    assert sorted_list == sorted(sorted_list), "Input must be sorted"  # a development-time sanity check
    # ... search logic ...

# INAPPROPRIATE — validating actual user input (NEVER do this)
def create_account(username, password):
    assert len(password) >= 8, "Password too short"    # WRONG — disappears entirely with python -O
    # ...

# The CORRECT way to validate input that genuinely matters
def create_account_correct(username, password):
    if len(password) < 8:
        raise ValueError("Password must be at least 8 characters")
    # ...

# Demonstrating the danger directly
def check_permission(user):
    assert user.is_admin, "Access denied"    # a SECURITY check using assert — genuinely dangerous
    grant_access()

# Running normally: python script.py     → the assert works, raises AssertionError for non-admins
# Running optimized: python -O script.py  → the assert is REMOVED — grant_access() runs for EVERYONE

# assert with a tuple is ALWAYS true (a classic, easy-to-miss gotcha)
# assert (condition, "message")    # WRONG — this is a non-empty TUPLE, which is always truthy!
assert condition, "message"          # CORRECT — comma separates the condition from the message, no parens needed
```

---

## 🚀 Real World Applications

- Verifying internal invariants during development and testing — conditions that should always hold if the code's own logic is correct
- Adding sanity checks inside a function's own implementation to catch a violated assumption early, close to its actual source
- Using `assert` extensively in test suites (many testing frameworks build directly on top of `assert` statements) where optimized mode is never a real concern
- Deliberately choosing `if`/`raise` (never `assert`) for anything involving user input validation, authentication, authorization, or business rule enforcement

---

## ⚖️ Advantages

- Extremely concise for expressing "this should always be true" checks during development, with automatic, free `AssertionError` generation.
- Assertions being strippable means they carry zero runtime performance cost in production when optimization is genuinely desired for pure internal-invariant checks.
- Widely used and well-understood as the foundation of Python's own testing culture (most test frameworks are built around `assert`).

---

## ⚠️ Limitations

- The ability to be globally stripped via `-O` makes `assert` fundamentally unsuitable for anything a program's actual correctness, security, or user-facing validation depends on.
- The `assert (condition, "message")` tuple gotcha is a genuinely common, silent bug — the assertion always passes regardless of the actual condition, since a non-empty tuple is always truthy.
- Overusing `assert` for legitimate error handling (rather than internal invariant-checking) blurs an important distinction and creates real risk if the code is ever run optimized.

---

## 🚨 Common Mistakes

- Using `assert` to validate user input, authentication state, or any security-relevant condition — this can be silently and completely disabled in production via `python -O`, a genuinely serious vulnerability if relied upon.
- Writing `assert (condition, "message")` with parentheses around both parts, unintentionally creating a tuple (which is always truthy) instead of separating the condition from its message with a comma.
- Assuming an `assert` failure will always be caught and handled — `AssertionError` is an ordinary exception; if genuinely uncaught, it crashes the program the same as any other unhandled exception, which is usually the intended behavior for a broken internal invariant.
- Not understanding that `-O` removes the *entire* `assert` statement, side effects and all — any function call inside the asserted expression also never runs when optimization is enabled.

---

## 📖 Further Reading

- Python documentation: "The Python Language Reference" — Section 7.3, "The assert statement"
- Python documentation: `-O` command-line option and `PYTHONOPTIMIZE` environment variable

---

## 💡 Wisdom from Mímir

Ask one question before writing an `assert`: if this check silently vanished entirely in production, would anything actually break or become insecure? If the honest answer is yes, it was never an `assert` in the first place — it's a real validation that belongs behind an explicit `if`/`raise`.

---

## 🔗 Related Notes

- [[Exception Handling - try, except, else, and finally]]
- [[Custom Exceptions in Python]]
- [[Python Codex]]
