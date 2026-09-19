---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Python
difficulty: Intermediate
tags:
  - oop
  - abc-module
  - abstract-methods
aliases:
  - ABC Module Python
  - abstractmethod Decorator
publish: true
permalink: python/abstract-base-classes
---

# <span class="rune">ᛟ</span> Abstract Base Classes

> *Without the ABC module, "this method must be implemented by subclasses" is just a comment and a prayer. With it, forgetting becomes a TypeError the moment someone tries to instantiate the incomplete class — not a bug discovered in production.*

---

## 🎯 Purpose

Python doesn't have a built-in `abstract` keyword the way Java does — instead, the standard library's `abc` module (Abstract Base Classes) provides `ABC` as a base class and `@abstractmethod` as a decorator, together enforcing that a subclass **must** override specific methods before it can even be instantiated. This brings Java-style abstract class enforcement to a language that would otherwise rely purely on convention and documentation.

---

## 🧠 Key Ideas

- Inheriting from `ABC` (from the `abc` module) and marking a method with `@abstractmethod` makes that method **mandatory** for any concrete subclass — attempting to instantiate a subclass that hasn't overridden every abstract method raises `TypeError` immediately, at instantiation time.
- Without `ABC`, Python has no way to prevent instantiating an "abstract" class directly, or to enforce that a subclass actually implements a required method — the pattern would rely purely on documentation and a `NotImplementedError` raised manually inside a placeholder method body (a much weaker, purely runtime-triggered safeguard).
- An abstract base class can still provide **concrete, shared implementation** for non-abstract methods, exactly like Java's abstract classes — it's not required to be 100% abstract.
- Python doesn't have a separate formal "interface" keyword/concept the way Java does — an `ABC` with *only* abstract methods and no shared state serves the same conceptual role as a Java interface, and this is the idiomatic way to express that intent in Python.
- `duck typing` (see [[Variables, Types, and Dynamic Typing]]) means Python code often doesn't *need* formal abstract base classes at all to achieve polymorphism — ABCs are reached for specifically when *enforcing* a contract matters more than just relying on convention.

---

## ⚙️ How It Works

`ABCMeta` (the metaclass `ABC` is built on) intercepts instantiation attempts and checks whether every method marked `@abstractmethod` on the class (or any of its parent classes) has actually been overridden with a concrete implementation — if even one remains unimplemented, attempting `SubClass()` raises `TypeError: Can't instantiate abstract class ... with abstract method ...` immediately, before the object is ever created. This is meaningfully stronger than the pure-convention alternative (a plain base class whose "abstract" methods just `raise NotImplementedError`), which only fails if and when that specific method actually gets *called* — potentially much later, and only on that specific code path.

```text
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self):
        pass

Shape()                  # TypeError — can't instantiate an ABC with unimplemented abstract methods, ever

class Circle(Shape):
    pass                   # forgot to implement area()

Circle()                    # STILL TypeError — Circle hasn't satisfied the contract either
```

---

## 💻 Examples

```python
from abc import ABC, abstractmethod

class Shape(ABC):
    def __init__(self, color):
        self.color = color            # concrete, SHARED implementation — ABCs can have real state

    @abstractmethod
    def area(self):                     # MUST be implemented by any concrete subclass
        pass

    def describe(self):                   # a concrete, non-abstract method — inherited as-is
        print(f"{self.color} shape with area {self.area()}")

# Shape("red")   # TypeError — Shape itself can NEVER be instantiated directly

class Circle(Shape):
    def __init__(self, color, radius):
        super().__init__(color)
        self.radius = radius

    def area(self):                        # satisfies the contract
        return 3.14159 * self.radius ** 2

c = Circle("red", 5)
c.describe()   # "red shape with area 78.53975"

class Incomplete(Shape):
    pass          # does NOT implement area()

# Incomplete("blue")   # TypeError: Can't instantiate abstract class Incomplete with abstract method area

# ABC as a pure "interface" — no shared state, only a contract
class Drawable(ABC):
    @abstractmethod
    def draw(self):
        pass

class Button(Drawable):
    def draw(self):
        print("Drawing a button")
```

---

## 🚀 Real World Applications

- Enforcing that every subclass of a plugin/handler base class implements the specific methods a framework or system depends on
- Defining a pure "interface"-style contract (only abstract methods, no shared implementation) for unrelated classes to implement independently
- Catching an incomplete subclass implementation immediately at instantiation time, rather than discovering the gap much later when a specific unimplemented method finally gets called
- Combining shared concrete behavior with a required abstract contract in a single base class, exactly as Java's abstract classes do

---

## ⚖️ Advantages

- Provides genuine, enforced structural guarantees in a language that otherwise leans heavily on convention and duck typing.
- Catches an incomplete subclass immediately, at instantiation, rather than allowing a broken object to exist until the specific missing method happens to be called.
- Can freely mix concrete shared implementation with abstract requirements, exactly like Java's abstract classes.

---

## ⚠️ Limitations

- Because Python's duck typing already provides a great deal of flexible polymorphism without any formal contract at all, ABCs add real ceremony that isn't always necessary or idiomatic for every situation.
- `ABCMeta`'s enforcement only checks that a method with the right *name* exists and has been overridden — it says nothing about parameter signatures matching, unlike some statically-typed languages' interface enforcement.
- Overusing ABCs where simple duck typing (or a lighter-weight `Protocol` from the `typing` module, for structural rather than nominal typing) would be more idiomatically "Pythonic."

---

## 🚨 Common Mistakes

- Defining "abstract" methods using only a docstring and a `raise NotImplementedError` inside a plain base class, without actually using `ABC`/`@abstractmethod` — this only fails when the specific method is actually called, not at instantiation, providing a much weaker guarantee than genuine ABC enforcement.
- Forgetting `@abstractmethod` requires the class to also inherit from `ABC` (or use `ABCMeta` as its metaclass directly) — the decorator alone, on a plain class, does nothing to enforce anything.
- Reaching for a full ABC when a much simpler duck-typing approach (or a `typing.Protocol`) would fit Python's idioms more naturally for the situation.
- Assuming ABC enforcement checks method signatures or return types — it only verifies that a method with the matching name has been overridden with *some* implementation, nothing more specific than that.

---

## 📖 Further Reading

- Python documentation: `abc` — Abstract Base Classes module reference
- Python documentation: `typing.Protocol` (structural typing, an alternative to nominal ABC-based contracts)

---

## 💡 Wisdom from Mímir

Reach for `ABC`/`@abstractmethod` specifically when you need Python to actually *enforce* that a contract is fulfilled, not merely document it — for the many cases where duck typing already gets the job done through simple convention, adding a formal ABC is often more ceremony than the situation actually calls for.

---

## 🔗 Related Notes

- [[Inheritance and super()]]
- [[Multiple Inheritance and the MRO]]
- [[Variables, Types, and Dynamic Typing]]
- [[Python Codex]]
