---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Python
difficulty: Intermediate
tags:
  - oop
  - dunder-methods
  - magic-methods
aliases:
  - Magic Methods Python
  - __str__ vs __repr__
publish: true
permalink: python/dunder-methods-init-str-repr-and-eq
---

# <span class="rune">ᛟ</span> Dunder Methods - __init__, __str__, __repr__, and __eq__

> *`print(obj)`, `obj == other`, `str(obj)` — none of these are special syntax hardcoded for every type. They're all just Python calling a dunder method on your object and using whatever it returns. Define the method, and you define the behavior.*

---

## 🎯 Purpose

"Dunder" methods (short for "double underscore," like `__init__`, `__str__`, `__eq__`) are Python's mechanism for making custom classes integrate naturally with the language's built-in syntax and functions — `print()`, `==`, `len()`, `+`, and many others all work by calling a specific dunder method on the object involved. This is conceptually similar to Java overriding `Object`'s `toString()`/`equals()`/`hashCode()`, but Python extends the same idea to operators and built-in functions far more broadly.

---

## 🧠 Key Ideas

- `__init__(self, ...)` — the initializer, called automatically when an object is created via `ClassName(...)` (covered in [[Classes and Objects in Python]]).
- `__str__(self)` — controls the "informal," human-readable string shown by `print(obj)` and `str(obj)`.
- `__repr__(self)` — controls the "official," unambiguous representation shown in the interactive interpreter and inside collections (e.g., printing a list of objects); by convention, should ideally look like valid Python code that could recreate the object.
- If `__str__` is not defined, Python falls back to using `__repr__` for `print()`/`str()` as well — this is why defining `__repr__` alone at minimum is often considered better practice than defining neither.
- `__eq__(self, other)` controls what `==` does for the class — without it, `==` defaults to reference identity (the same as Java's default `equals()`), comparing whether two variables point to the exact same object rather than comparing their content.

---

## ⚙️ How It Works

Every one of Python's built-in operators and functions is implemented, for user-defined classes, as a lookup for a specific dunder method on the object — `print(obj)` internally calls `str(obj)`, which calls `obj.__str__()`; `obj1 == obj2` calls `obj1.__eq__(obj2)`. This is precisely why defining these methods on a custom class makes it behave naturally with built-in syntax: there's no special-casing for user classes anywhere in the language, just this one consistent mechanism.

```text
print(my_point)          # → str(my_point)         → my_point.__str__()
my_point == other_point   # → my_point.__eq__(other_point)
repr(my_point)              # → my_point.__repr__()
```

---

## 💻 Examples

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __str__(self):
        return f"({self.x}, {self.y})"                # human-readable

    def __repr__(self):
        return f"Point(x={self.x}, y={self.y})"          # unambiguous, code-like

    def __eq__(self, other):
        if not isinstance(other, Point):
            return NotImplemented
        return self.x == other.x and self.y == other.y

    def __hash__(self):                                    # needed if the class should be usable in sets/dict keys
        return hash((self.x, self.y))

p1 = Point(1, 2)
p2 = Point(1, 2)

print(p1)          # (1, 2)                — via __str__
print(repr(p1))     # Point(x=1, y=2)        — via __repr__

print(p1 == p2)      # True — content comparison, via __eq__
print(p1 is p2)       # False — DIFFERENT objects in memory, even though they're "equal"

# Without __str__, print() falls back to __repr__
class Simple:
    def __repr__(self):
        return "Simple()"

print(Simple())   # "Simple()" — used __repr__ since __str__ wasn't defined

# Without EITHER, Python's default is unhelpful
class Bare:
    pass

print(Bare())   # something like <__main__.Bare object at 0x...>

# Other common dunders
class Money:
    def __init__(self, amount): self.amount = amount
    def __add__(self, other): return Money(self.amount + other.amount)   # enables the + operator
    def __len__(self): return int(self.amount)                            # enables len()

m1, m2 = Money(10), Money(5)
combined = m1 + m2     # calls m1.__add__(m2)
```

---

## 🚀 Real World Applications

- Defining `__repr__` on essentially every custom class to make debugging and interactive interpreter output genuinely useful
- Defining `__eq__` (and `__hash__` alongside it, if the object should ever be used as a dict key or set element) whenever content-based equality is needed
- Implementing arithmetic-like operators (`__add__`, `__sub__`, `__mul__`) for custom numeric or vector-like types
- Making a custom class support `len()`, iteration (`__iter__`), or indexing (`__getitem__`) so it behaves like a native built-in collection where appropriate

---

## ⚖️ Advantages

- Dunder methods let custom classes integrate seamlessly with Python's built-in operators and functions, rather than requiring awkward custom method names for basic operations.
- Defining `__repr__` costs very little and dramatically improves debugging output, print statements, and interactive interpreter usability.
- The consistent, predictable naming convention (`__method__`) makes it straightforward to discover which dunder a given piece of built-in syntax actually maps to.

---

## ⚠️ Limitations

- Defining `__eq__` on a class **disables** the default `__hash__` inherited from `object` (Python considers a mutable, custom-equality class potentially unsafe to hash by default) — meaning `__hash__` often needs to be explicitly redefined too if the object should be usable in sets/dict keys.
- Overloading operators (`__add__`, `__mul__`, etc.) too liberally, especially in ways that don't match their conventional mathematical meaning, can make code confusing rather than more expressive.
- Getting `__eq__` and `__hash__` inconsistent with each other (equal objects with different hashes, or vice versa) causes the exact same category of subtle collection bugs as in Java, discussed generally in [[Object Equality - equals(), ==, and hashCode()]] from the Java Codex's own coverage of this shared concept.

---

## 🚨 Common Mistakes

- Defining `__eq__` without also defining `__hash__`, then being surprised the object silently becomes unhashable (`TypeError: unhashable type`) when used as a dict key or set element.
- Relying on the default `__repr__`/`__str__` (an unhelpful memory-address-based string) instead of defining at least `__repr__` on custom classes, making debugging output far less useful than it could be.
- Returning `False` from `__eq__` instead of `NotImplemented` when comparing against an incompatible type — `NotImplemented` allows Python to correctly try the other object's `__eq__` as a fallback, which a bare `False` prevents.
- Confusing `__str__` (for humans, via `print()`) with `__repr__` (for developers/debugging, ideally code-like) and only implementing one when both serve genuinely different purposes.

---

## 📖 Further Reading

- Python documentation: "Data Model" — the full dunder method reference
- Python documentation: `object.__repr__` and `object.__str__`

---

## 💡 Wisdom from Mímir

Define `__repr__` on nearly every class you write, as a near-automatic habit, even if `__str__` never gets added — it's the fallback Python reaches for whenever `__str__` is missing, and a genuinely useful `__repr__` pays for itself the very first time you're debugging in an interactive shell and print a list of these objects.

---

## 🔗 Related Notes

- [[Classes and Objects in Python]]
- [[Class Methods, Static Methods, and Properties]]
- [[Python Codex]]
