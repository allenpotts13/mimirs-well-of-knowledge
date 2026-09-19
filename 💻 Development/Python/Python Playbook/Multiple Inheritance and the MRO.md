---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Python
difficulty: Advanced
tags:
  - oop
  - multiple-inheritance
  - mro
  - c3-linearization
aliases:
  - Method Resolution Order
  - Diamond Problem Python
publish: true
permalink: python/multiple-inheritance-and-the-mro
---

# <span class="rune">ᛟ</span> Multiple Inheritance and the MRO

> *When two parent classes both define the same method, Python doesn't guess or throw an error — it consults one specific, deterministic, computable order, and `ClassName.__mro__` will tell you exactly what that order is, every time.*

---

## 🎯 Purpose

Because Python allows genuine multiple inheritance (`class C(A, B):`) — something Java's `extends` deliberately forbids — it needs a precise, predictable rule for resolving method lookups when multiple parent classes might define the same method name. That rule is the **Method Resolution Order (MRO)**, computed using the **C3 linearization algorithm**, and it's a topic that comes up specifically because Java's simpler single-inheritance model never has to solve this problem at all.

---

## 🧠 Key Ideas

- The MRO is the specific, linear order Python searches through a class's ancestors when looking up a method or attribute — visible directly via `ClassName.__mro__` or `ClassName.mro()`.
- Python computes the MRO using **C3 linearization**, which guarantees: a class always comes before its parents in the order, and the relative order of parents as originally listed in the class definition is preserved.
- The classic "**diamond problem**" — where class `D` inherits from both `B` and `C`, and both `B` and `C` inherit from a common `A` — is resolved deterministically by the MRO, ensuring `A`'s methods are only considered once, in a consistent position, rather than ambiguously duplicated.
- `super()` in a multiple-inheritance context doesn't necessarily mean "my direct parent" — it means "the next class in the MRO after the current one," which can be a sibling class entirely unrelated by a naive reading of the inheritance diagram.
- If C3 linearization cannot produce a consistent, valid order (a genuinely contradictory inheritance structure), Python raises `TypeError: Cannot create a consistent method resolution order` at class definition time — a real safeguard against genuinely nonsensical hierarchies.

---

## ⚙️ How It Works

For a simple, single-inheritance chain, the MRO is exactly what you'd expect: the class itself, then its parent, then its grandparent, and so on up to `object`. Multiple inheritance is where it gets genuinely more interesting: C3 linearization merges the MROs of every listed parent class, plus the list of parents itself, while preserving two guarantees — a class always precedes its own parents, and parents retain their originally-declared left-to-right order relative to each other. This means `super()` calls chain through the *entire* MRO, not just up a single, simple inheritance line — a call to `super().method()` in class `B` might actually land in sibling class `C`, if that's genuinely where the MRO places it next.

```text
class A: pass
class B(A): pass
class C(A): pass
class D(B, C): pass         # the "diamond" — B and C BOTH inherit from A

D.__mro__
# (D, B, C, A, object) — D first, then B, then C (declaration order preserved), THEN A (only ONCE), then object
```

---

## 💻 Examples

```python
class A:
    def greet(self):
        print("Hello from A")

class B(A):
    def greet(self):
        print("Hello from B")
        super().greet()

class C(A):
    def greet(self):
        print("Hello from C")
        super().greet()

class D(B, C):
    def greet(self):
        print("Hello from D")
        super().greet()

print(D.__mro__)
# (D, B, C, A, object) — the actual, computed lookup order

d = D()
d.greet()
# Hello from D
# Hello from B
# Hello from C   ← super() in B goes to C, NOT directly to A — because that's the NEXT class in the MRO
# Hello from A
# (A is visited only ONCE, even though both B and C inherit from it)

# A genuinely inconsistent hierarchy — Python refuses to create it
class X: pass
class Y: pass
# class Z(X, Y, Y): pass   # or other genuinely contradictory orderings —
                              # raises TypeError: Cannot create a consistent MRO

# Practical use — mixins, relying on cooperative super() calls
class LoggingMixin:
    def save(self):
        print("Logging save operation")
        super().save()      # cooperatively continues the MRO chain

class Model:
    def save(self):
        print("Saving to database")

class LoggedModel(LoggingMixin, Model):
    pass

LoggedModel().save()
# Logging save operation
# Saving to database
```

---

## 🚀 Real World Applications

- Building **mixin classes** — small, focused classes providing one specific piece of reusable behavior, combined via multiple inheritance and cooperative `super()` calls
- Diagnosing "why did this method call the wrong version" bugs in a multiple-inheritance hierarchy by inspecting `ClassName.__mro__` directly rather than guessing
- Understanding library and framework code (many Python frameworks use mixin-based multiple inheritance extensively) that relies on cooperative `super()` chaining across sibling classes
- Recognizing when a class hierarchy has become too complex to reason about, and simplifying toward composition or a shallower hierarchy instead

---

## ⚖️ Advantages

- The MRO provides a fully deterministic, computable, and inspectable resolution order — nothing about multiple inheritance in Python is left to guesswork or implementation-defined behavior.
- Cooperative multiple inheritance via mixins enables composing behavior from several independent, reusable pieces without Java's more restrictive single-inheritance-plus-interfaces model.
- `ClassName.__mro__` makes debugging method resolution issues genuinely possible — the exact order is always directly inspectable.

---

## ⚠️ Limitations

- The MRO and C3 linearization are widely regarded as one of the more genuinely advanced and non-obvious corners of Python, and deeply nested multiple-inheritance hierarchies can become very difficult to reason about even with the MRO fully understood.
- `super()`'s "next in the MRO" behavior (rather than "my direct parent") is a frequent source of confusion for anyone assuming it works the same simple way it does in single-inheritance-only languages like Java.
- A genuinely contradictory inheritance structure raises a `TypeError` at class definition time, which can be a confusing error to debug without already understanding what C3 linearization is trying to guarantee.

---

## 🚨 Common Mistakes

- Assuming `super()` inside a multiple-inheritance class always refers to "the first listed parent" — it actually refers to whatever comes next in the *computed MRO*, which can be a different class than a naive reading of the inheritance list would suggest.
- Building deep, complex multiple-inheritance hierarchies without ever checking `ClassName.__mro__`, then being confused when a method call resolves to an unexpected implementation.
- Not making mixin classes properly "cooperative" (i.e., forgetting to call `super().method()` inside them), breaking the MRO chain for any class further down the line that expected it to continue.
- Reaching for multiple inheritance by default when a simpler composition-based design, or Python's `typing.Protocol` for structural typing, would express the same intent more simply.

---

## 📖 Further Reading

- Python documentation: "The Python 2.3 Method Resolution Order" (the original C3 linearization writeup, still the canonical reference)
- Python documentation: `super()` built-in function reference

---

## 💡 Wisdom from Mímir

Whenever a multiple-inheritance method call resolves somewhere unexpected, resist guessing — print `ClassName.__mro__` immediately and read the actual, computed order directly. It's fully deterministic and always inspectable; there's never a need to reason about it purely from the inheritance diagram alone.

---

## 🔗 Related Notes

- [[Inheritance and super()]]
- [[Abstract Base Classes]]
- [[Python Codex]]
