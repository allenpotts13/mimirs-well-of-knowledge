---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Python
difficulty: Intermediate
tags:
  - error-handling
  - exceptions
  - try-except
aliases:
  - Python try-except-else-finally
  - EAFP vs LBYL
publish: true
permalink: python/exception-handling-try-except-else-and-finally
---

# <span class="rune">ᛟ</span> Exception Handling - try, except, else, and finally

> *Python's own culture has a name for its preferred error-handling philosophy: "easier to ask forgiveness than permission." Try the risky thing first, catch the failure if it happens — rather than checking every precondition beforehand and hoping nothing changes in between.*

---

## 🎯 Purpose

Python's exception handling uses `try`/`except`/`else`/`finally` — structurally similar to Java's `try`/`catch`/`finally`, but with one addition Java doesn't have (`else`) and one major philosophical difference: Python has **no checked exceptions** at all. Every exception in Python is effectively "unchecked" — nothing in the language forces a caller to handle or declare any exception, ever.

---

## 🧠 Key Ideas

- `try:` wraps risky code; `except ExceptionType:` catches a specific exception type (or a tuple of types); `else:` runs **only if the try block completed with no exception at all**; `finally:` always runs, regardless of what happened.
- Python has **no checked exceptions** — unlike Java's compiler-enforced distinction (see the Java Codex's [[Checked vs Unchecked Exceptions]]), every Python exception behaves like Java's unchecked ones: nothing forces a caller to catch or declare anything.
- Catching a broad `except Exception:` (or worse, a bare `except:`) is strongly discouraged — it silently catches things you likely didn't intend to catch (including, for a bare `except:`, even `KeyboardInterrupt` and `SystemExit`), masking real bugs.
- Python's `else` clause on `try` is genuinely useful and has no Java equivalent — it separates "code that might fail" (in `try`) from "code that should only run on success" (in `else`), keeping the `try` block itself as narrow as possible.
- **EAFP** ("Easier to Ask Forgiveness than Permission") is Python's idiomatic style: attempt the operation directly inside a `try`, and handle the exception if it fails — rather than **LBYL** ("Look Before You Leap"), checking every precondition first. EAFP is generally preferred in Python, partly because it avoids race conditions between the check and the actual operation.

---

## ⚙️ How It Works

Because Python has no checked exceptions, any function can raise any exception at any time with zero compiler-level warning to its callers — documentation and testing carry the entire burden of communicating what a function might raise, unlike Java's `throws` clause making it part of the compiled contract. The `else` clause exists specifically to narrow the `try` block to only the code that's actually expected to potentially fail — code that should run *after* success, but that you don't want accidentally caught by the same `except` block if it happens to raise a similar exception type itself.

```text
try:
    risky_operation()          # ONLY the code that might actually fail belongs here
except SpecificError:
    handle_the_failure()
else:
    definitely_succeeded()      # runs ONLY if try raised NOTHING — kept separate on purpose
finally:
    always_runs()                 # cleanup — guaranteed, no matter what happened above
```

---

## 💻 Examples

```python
# Basic try/except
try:
    result = 10 / 0
except ZeroDivisionError:
    print("Cannot divide by zero")

# Multiple except blocks — most specific first
try:
    value = int(input("Enter a number: "))
    result = 10 / value
except ValueError:
    print("That wasn't a valid number")
except ZeroDivisionError:
    print("Cannot divide by zero")

# Catching multiple types in one block
try:
    risky_operation()
except (ValueError, TypeError) as e:
    print(f"Something went wrong: {e}")

# try/except/else/finally — the FULL pattern
try:
    f = open("data.txt")
except FileNotFoundError:
    print("File not found")
else:
    content = f.read()      # ONLY runs if open() succeeded — kept OUT of the try's exception scope
    f.close()
finally:
    print("Done attempting to read the file")   # ALWAYS runs

# EAFP — Python's preferred style
def get_value(d, key):
    try:
        return d[key]
    except KeyError:
        return None

# LBYL — the alternative style, generally less idiomatic in Python
def get_value_lbyl(d, key):
    if key in d:              # a separate check BEFORE the actual operation
        return d[key]
    return None

# NEVER do this — a bare except catches EVERYTHING, including KeyboardInterrupt
# try:
#     risky_operation()
# except:                      # AVOID — catches literally everything, hides real bugs
#     pass

# Raising your own exceptions
def withdraw(balance, amount):
    if amount > balance:
        raise ValueError(f"Insufficient funds: tried to withdraw {amount}, balance is {balance}")
    return balance - amount
```

---

## 🚀 Real World Applications

- Using EAFP to attempt a dictionary/attribute access directly and catch `KeyError`/`AttributeError`, rather than checking existence first with a separate condition
- Using `else` to keep post-success logic clearly separated from the specific operation being risked in the `try` block
- Guaranteeing cleanup code (closing a file, releasing a lock) runs via `finally`, or preferring a [[Context Managers and the with Statement|context manager]] when the resource supports one
- Catching specific, narrow exception types rather than broad catch-alls, to avoid silently masking bugs unrelated to the specific failure being anticipated

---

## ⚖️ Advantages

- No checked exceptions means far less boilerplate than Java's `throws` declarations propagating up through every intermediate method.
- `else` cleanly separates "might fail" code from "only runs on success" code, keeping `try` blocks narrowly scoped to just the actual risk.
- EAFP style often produces cleaner code than LBYL, especially in concurrent contexts where a separate existence check could become stale before the actual operation runs.

---

## ⚠️ Limitations

- With no checked exceptions and no compiler enforcement at all, discovering what a function might raise requires reading its documentation or source code — there's no equivalent of Java's `throws` clause as a compiled, guaranteed contract.
- Overly broad `except Exception:` (or a bare `except:`) blocks are extremely easy to write and can silently hide real bugs that should have propagated and been noticed.
- EAFP can make debugging slightly less obvious for developers newer to Python, since the "happy path" logic is sometimes buried inside a `try` block rather than guarded by explicit upfront checks.

---

## 🚨 Common Mistakes

- Using a bare `except:` (with no exception type specified at all), which catches literally everything — including `KeyboardInterrupt` and `SystemExit` — making the program impossible to interrupt cleanly and hiding unrelated bugs.
- Catching `Exception` broadly "just to be safe," masking programming errors that should have surfaced and been fixed rather than silently swallowed.
- Putting code that should only run on success directly inside the `try` block instead of `else`, accidentally exposing it to the same `except` handler even if it raises a similar exception type for an unrelated reason.
- Forgetting Python provides no compile-time warning about what a function might raise — relying entirely on documentation, testing, and experience to know what to actually catch.

---

## 📖 Further Reading

- Python documentation: "The Python Tutorial" — Section 8, "Errors and Exceptions"
- Python glossary: "EAFP" and "LBYL"

---

## 💡 Wisdom from Mímir

Catch the narrowest, most specific exception type the situation actually calls for — never a bare `except:`, and reach for broad `except Exception:` only as a genuinely last resort, with a clear reason. Every overly broad catch block is a bug detector quietly turned off, and you won't know what it silently swallowed until something goes wrong somewhere else, much later, for reasons that trace back to here.

---

## 🔗 Related Notes

- [[Custom Exceptions in Python]]
- [[assert and Defensive Programming]]
- [[Context Managers and the with Statement]]
- [[Python Codex]]
