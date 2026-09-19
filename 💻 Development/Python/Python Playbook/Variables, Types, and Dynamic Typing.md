---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Python
difficulty: Beginner
tags:
  - python-fundamentals
  - dynamic-typing
  - duck-typing
aliases:
  - Duck Typing
  - Dynamic vs Static Typing
publish: true
permalink: python/variables-types-and-dynamic-typing
---

# <span class="rune">ᛟ</span> Variables, Types, and Dynamic Typing

> *A Python variable doesn't hold a type — it holds a name tag, temporarily stuck onto whatever object happens to be walking by. The object has a type. The name never does.*

---

## 🎯 Purpose

Python is **dynamically typed**: a variable's type isn't declared or fixed — it's simply whatever the current object it references happens to be, and that can change freely over the variable's lifetime. This is fundamentally different from Java's static typing, and it's the source of both Python's famous flexibility and a specific class of bugs that only surface at runtime instead of compile time.

---

## 🧠 Key Ideas

- A variable in Python is a **name bound to an object** — `x = 5` doesn't create a box labeled `x` holding `5`; it points the name `x` at an `int` object holding `5` somewhere in memory.
- The same name can be rebound to a completely different type at any time: `x = 5` then later `x = "hello"` is entirely legal — the *variable* was never typed, only the objects it pointed to.
- Python uses **duck typing**: "if it walks like a duck and quacks like a duck, it's a duck" — code generally cares whether an object *supports* an operation, not what specific type it declares itself to be.
- `type(x)` returns an object's actual runtime type; `isinstance(x, SomeType)` checks whether an object is of a given type (or subtype) — the idiomatic way to type-check when it's genuinely needed.
- Optional **type hints** (`x: int = 5`, added in Python 3.5+) document intended types for tooling and readability, but are **not enforced at runtime** by the language itself — they're a convention checked by external tools like `mypy`, not a compiler guarantee.

---

## ⚙️ How It Works

When `x = 5` executes, Python creates (or reuses) an `int` object representing `5`, and the name `x` in the current namespace is bound to it — nothing about `x` itself carries type information; the type belongs entirely to the object. Reassigning `x = "hello"` simply rebinds the same name to a different object of a different type — there's no static declaration anywhere preventing this, which is precisely why type errors in Python (calling a string method on what turns out to be an integer) surface only when that specific line actually executes, not before the program runs at all.

```text
x = 5          # name "x" bound to an int object
x = "hello"    # name "x" REBOUND to a completely different (str) object — perfectly legal
x = [1, 2, 3]  # rebound again, now to a list — the name itself was NEVER typed
```

---

## 💻 Examples

```python
x = 5
print(type(x))       # <class 'int'>
x = "hello"
print(type(x))       # <class 'str'> — same name, entirely different type now

# Duck typing — no type declaration needed, just supported behavior
def make_it_quack(thing):
    thing.quack()      # works for ANYTHING with a .quack() method, regardless of its declared type

class Duck:
    def quack(self): print("Quack!")

class Person:
    def quack(self): print("I'm quacking like a duck!")

make_it_quack(Duck())    # works
make_it_quack(Person())  # ALSO works — duck typing doesn't care about the actual class

# isinstance() — the idiomatic type check when one is genuinely needed
def process(value):
    if isinstance(value, (int, float)):
        return value * 2
    return value

# Optional type hints — documentation for humans and tools, NOT enforced at runtime
def greet(name: str) -> str:
    return "Hello, " + name

greet(42)   # runs WITHOUT error at runtime — Python does not check the hint itself
```

---

## 🚀 Real World Applications

- Writing functions that work with any object supporting the needed behavior, without rigid type constraints (the essence of duck typing)
- Using type hints throughout a codebase for IDE autocomplete and static analysis via `mypy`, while still relying on tests to catch actual type mismatches at runtime
- Understanding why a Python function can silently accept an "unexpected" type and only fail deep inside its logic, rather than at the call site
- Using `isinstance()` for genuine runtime type checks, rather than the less flexible `type(x) == SomeType` (which incorrectly excludes subclasses)

---

## ⚖️ Advantages

- Extremely flexible — the same code can work with many different types as long as they support the needed operations, with no boilerplate type declarations.
- Rapid prototyping and scripting are faster without the ceremony of declaring types everywhere.
- Duck typing enables a natural, flexible style of polymorphism without requiring formal inheritance or interface implementation.

---

## ⚠️ Limitations

- Type errors that a statically-typed language like Java would catch at compile time only surface in Python at runtime, potentially deep into program execution.
- Code relying heavily on duck typing can be harder to understand without good documentation or type hints, since it's not always obvious what a function actually expects.
- Type hints add real documentation value but provide **zero runtime protection** on their own — a common point of confusion for anyone assuming they behave like Java's type system.

---

## 🚨 Common Mistakes

- Assuming type hints are enforced like a statically-typed language's type system — they're purely advisory unless a separate tool like `mypy` is run against the code.
- Using `type(x) == SomeType` for a type check instead of `isinstance(x, SomeType)`, which incorrectly excludes valid subclasses.
- Rebinding a variable to an unrelated type partway through a function and losing track of what it currently holds, especially in longer functions.
- Writing code that implicitly assumes a specific type without validating it, then getting a confusing `AttributeError` or `TypeError` far from where the actual bad value originated.

---

## 📖 Further Reading

- Python documentation: "The Python Tutorial" — Section 3, "An Informal Introduction to Python"
- PEP 484: "Type Hints"

---

## 💡 Wisdom from Mímir

When a Python bug looks like "this should have failed sooner," remember there's no compiler standing between you and runtime the way there is in Java — dynamic typing means many mistakes only reveal themselves the moment the specific offending line actually executes, which can be well after the actual root cause.

---

## 🔗 Related Notes

- [[Functions and Default Arguments]]
- [[Dictionaries]]
- [[Python Codex]]
