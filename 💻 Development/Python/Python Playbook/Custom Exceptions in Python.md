---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Python
difficulty: Intermediate
tags:
  - error-handling
  - custom-exceptions
  - exception-chaining
aliases:
  - Custom Exception Class Python
  - raise ... from ...
publish: true
permalink: python/custom-exceptions-in-python
---

# <span class="rune">ᛟ</span> Custom Exceptions in Python

> *`raise NewError("message") from original_error` doesn't just attach a note — it builds a real, permanent chain Python's own traceback printer understands and displays, showing exactly which exception caused which, in order.*

---

## 🎯 Purpose

Custom exceptions in Python are defined by subclassing `Exception` (or a more specific existing built-in exception), giving domain-specific failures clear, meaningful names — the same underlying motivation as Java's custom exceptions. Python's `raise ... from ...` syntax provides built-in, first-class support for exception chaining, making the relationship between an original failure and a wrapping exception explicit and visible in the traceback.

---

## 🧠 Key Ideas

- A custom exception is simply a class inheriting from `Exception` (or an appropriate built-in subclass like `ValueError`) — often just a few lines, sometimes literally just `class MyError(Exception): pass`.
- `raise NewException("message") from original_exception` explicitly chains the new exception to the one that caused it — Python's traceback then clearly shows "The above exception was the direct cause of the following exception," preserving the full causal chain for debugging.
- Without an explicit `from`, an exception raised inside an `except` block is still automatically chained (shown as "During handling of the above exception, another exception occurred") — but `from` makes the *intended* causal relationship explicit rather than incidental.
- Custom exceptions can carry additional data beyond a message, by overriding `__init__` and storing extra attributes — exactly the same pattern as Java's custom exceptions carrying structured fields.
- Since Python has no checked/unchecked distinction (see [[Exception Handling - try, except, else, and finally]]), the design decision Java requires (checked vs. `RuntimeException`) simply doesn't exist — every Python exception behaves uniformly, and the only real decision is which built-in exception (if any) to inherit from.

---

## ⚙️ How It Works

`raise NewException(...) from original` sets the new exception's `__cause__` attribute to `original` — Python's default traceback formatter specifically checks for this attribute and, when present, prints both the original exception's full traceback and the new one's, connected by an explicit line explaining the causal relationship. This is functionally similar to Java's exception chaining via the two-argument `Throwable` constructor, but built directly into Python's `raise` syntax itself rather than requiring a specific constructor pattern.

```text
try:
    parse_file("data.csv")
except IOError as original:
    raise DataProcessingError("Failed to process data.csv") from original

# Resulting traceback shows BOTH exceptions, clearly connected:
#   Traceback (most recent call last): ... IOError: ...
#   The above exception was the direct cause of the following exception:
#   Traceback (most recent call last): ... DataProcessingError: Failed to process data.csv
```

---

## 💻 Examples

```python
# A simple custom exception
class InsufficientFundsError(Exception):
    pass

def withdraw(balance, amount):
    if amount > balance:
        raise InsufficientFundsError(f"Cannot withdraw {amount}, balance is {balance}")
    return balance - amount

try:
    withdraw(100, 500)
except InsufficientFundsError as e:
    print(f"Transaction failed: {e}")

# A custom exception carrying additional structured data
class InsufficientFundsError(Exception):
    def __init__(self, message, shortfall):
        super().__init__(message)
        self.shortfall = shortfall

def withdraw_detailed(balance, amount):
    if amount > balance:
        shortfall = amount - balance
        raise InsufficientFundsError(
            f"Short by {shortfall}", shortfall)
    return balance - amount

try:
    withdraw_detailed(100, 150)
except InsufficientFundsError as e:
    print(e)                    # "Short by 50"
    print(e.shortfall)            # 50 — structured data, not just a message string

# Exception chaining with "from" — preserving the original cause
class DataProcessingError(Exception):
    pass

def process_file(path):
    try:
        with open(path) as f:
            return f.read()
    except FileNotFoundError as original:
        raise DataProcessingError(f"Could not process {path}") from original

# A small custom exception hierarchy
class AppError(Exception):
    """Base exception for this application."""

class ValidationError(AppError):
    pass

class DatabaseError(AppError):
    pass

try:
    raise ValidationError("Invalid input")
except AppError as e:              # catches ValidationError, DatabaseError, or any future subclass
    print(f"Application error: {e}")

# Suppressing chaining explicitly when the original cause genuinely isn't relevant
def convert(value):
    try:
        return int(value)
    except ValueError:
        raise TypeError("Expected a number") from None   # "from None" hides the ORIGINAL traceback entirely
```

---

## 🚀 Real World Applications

- Defining a small hierarchy of application-specific exceptions (a common base class, plus specific subtypes) so callers can catch broadly or narrowly as needed
- Using `raise ... from ...` when wrapping a lower-level exception (a file I/O error, a network error) into a higher-level, more meaningful application exception
- Carrying structured data on a custom exception so calling code can react programmatically rather than parsing an error message string
- Using `from None` deliberately when an original exception's traceback would be confusing or irrelevant noise for the actual caller

---

## ⚖️ Advantages

- Custom exceptions dramatically improve error message clarity and enable precise, targeted `except` handling compared to generic built-in exceptions alone.
- `raise ... from ...` gives Python first-class, built-in exception chaining without any special constructor pattern needed — simpler syntactically than Java's equivalent.
- A small custom exception hierarchy lets calling code choose exactly how broadly or narrowly to catch failures, from one specific subtype up to a shared base class.

---

## ⚠️ Limitations

- Because Python has no checked exceptions, nothing in the language reminds a developer what a function might raise — good docstrings and tests carry that entire burden.
- Overly deep or overly granular custom exception hierarchies can add more classes than the clarity benefit justifies for a genuinely small application.
- `from None` suppresses potentially useful debugging context — it should be used deliberately, not reflexively, only when the original traceback would genuinely mislead rather than help.

---

## 🚨 Common Mistakes

- Raising a new exception inside an `except` block without `from`, losing the opportunity to make the causal relationship explicit (Python still shows an implicit chain, but `from` communicates clear, deliberate intent).
- Catching `Exception` broadly instead of a specific custom exception (or its shared base class), losing the precision a custom exception hierarchy was designed to provide.
- Not giving a custom exception hierarchy a shared base class, missing the ability for calling code to catch "any error from this specific subsystem" in one `except` clause.
- Overusing `from None` reflexively, discarding genuinely useful original tracebacks that would have helped diagnose the real underlying cause.

---

## 📖 Further Reading

- Python documentation: "The Python Tutorial" — Section 8.6, "Exception Chaining"
- Python documentation: "Built-in Exceptions" — the full hierarchy of built-in exception types worth inheriting from

---

## 💡 Wisdom from Mímir

Whenever wrapping a lower-level exception into a more meaningful custom one, use `raise ... from ...` explicitly rather than relying on Python's implicit chaining — it costs three words and turns "why did this actually happen" from an archaeology exercise through nested tracebacks into something the traceback itself states outright.

---

## 🔗 Related Notes

- [[Exception Handling - try, except, else, and finally]]
- [[Classes and Objects in Python]]
- [[Python Codex]]
