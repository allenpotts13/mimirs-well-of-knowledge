---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Python
difficulty: Beginner
tags:
  - oop
  - inheritance
  - super
aliases:
  - Python super()
  - extends Equivalent Python
publish: true
permalink: python/inheritance-and-super
---

# <span class="rune">ᛟ</span> Inheritance and super()

> *Unlike Java, Python never forces a subclass to call its parent's constructor — which means the parent's `__init__` genuinely might never run at all, silently, unless you remember to call `super().__init__()` yourself.*

---

## 🎯 Purpose

Python supports inheritance via `class Subclass(Superclass):`, letting a class acquire the attributes and methods of another — the same "is-a" relationship modeling as Java's `extends`. `super()` provides access to the parent class's methods, most commonly to invoke the parent's `__init__` from within a subclass's own `__init__`. Unlike Java, Python doesn't enforce this call at all — a genuinely important difference.

---

## 🧠 Key Ideas

- `class Dog(Animal):` makes `Dog` a subclass of `Animal`, inheriting its methods and attributes automatically.
- `super().__init__(...)` calls the parent class's `__init__` — but unlike Java's `super(...)`, it is **not required** to be the first statement, and it is **not automatically called** if omitted; a subclass's `__init__` can simply skip calling the parent's entirely (though this usually indicates a bug, since inherited attributes may never get set up).
- Python supports genuine **multiple inheritance** directly (`class C(A, B):`) — unlike Java's single-inheritance restriction for classes — resolved through the **Method Resolution Order** (MRO), covered in [[Multiple Inheritance and the MRO]].
- Overriding a method in a subclass is automatic and requires no special annotation — simply redefining a method with the same name in the subclass replaces the parent's version for instances of that subclass, with no `@Override`-equivalent keyword required (though a `# noqa` linter comment or type-checker annotation can serve a similar documentation purpose).
- `isinstance(obj, ParentClass)` returns `True` for instances of any subclass too, exactly matching Java's polymorphic `instanceof` behavior.

---

## ⚙️ How It Works

When Python creates a `Dog` instance, `Dog.__init__` runs — and if that method never explicitly calls `super().__init__(...)`, `Animal.__init__` simply never executes at all, silently, with no error or warning. This is meaningfully different from Java, where a subclass constructor is required (explicitly or implicitly) to invoke a superclass constructor before doing anything else — Python trusts the developer to remember, and forgetting is a genuinely common source of bugs where inherited attributes are missing because the parent's setup logic was skipped entirely.

```text
class Animal:
    def __init__(self, name):
        self.name = name             # sets up "name" — but ONLY IF this method actually runs

class Dog(Animal):
    def __init__(self, name, breed):
        super().__init__(name)        # MUST be called explicitly — Python will NOT do this for you
        self.breed = breed

class BrokenDog(Animal):
    def __init__(self, name, breed):
        # forgot super().__init__(name) — self.name is NEVER SET, silently
        self.breed = breed
```

---

## 💻 Examples

```python
class Animal:
    def __init__(self, name):
        self.name = name

    def speak(self):
        print(f"{self.name} makes a sound")

class Dog(Animal):
    def __init__(self, name, breed):
        super().__init__(name)      # explicitly calls Animal's __init__
        self.breed = breed

    def speak(self):                  # OVERRIDES Animal's speak() — no special syntax needed
        super().speak()                # still calls Animal's ORIGINAL version too, if desired
        print(f"{self.name} says Woof!")

rex = Dog("Rex", "Labrador")
rex.speak()
# Rex makes a sound
# Rex says Woof!

print(isinstance(rex, Animal))   # True — Dog IS-A Animal
print(isinstance(rex, Dog))        # True

# Multiple inheritance — Python allows this directly
class Swimmer:
    def swim(self): print("Swimming")

class Flyer:
    def fly(self): print("Flying")

class Duck(Animal, Swimmer, Flyer):    # inherits from THREE classes at once
    pass

duck = Duck("Donald")
duck.speak()   # from Animal
duck.swim()     # from Swimmer
duck.fly()       # from Flyer
```

---

## 🚀 Real World Applications

- Modeling a family of related classes sharing common initialization logic and behavior, with subclasses extending or customizing specific pieces
- Combining multiple independent behaviors via multiple inheritance (a "mixin" pattern) rather than Java's more restrictive single-inheritance-plus-interfaces model
- Using `super().method()` inside an overriding method to extend rather than completely replace the parent's behavior
- Double-checking that every subclass `__init__` correctly calls `super().__init__(...)` during code review, since Python provides no compiler-level safeguard against forgetting it

---

## ⚖️ Advantages

- Genuine multiple inheritance offers more compositional flexibility than Java's single-inheritance-plus-interfaces model, particularly for mixin-style code reuse.
- `super()` with no arguments (Python 3's simplified syntax) is more concise than the equivalent explicit parent-class reference some other languages (and Python 2) require.
- No forced constructor-call ordering gives more flexibility for unusual initialization patterns, at the cost of the safety Java's mandatory `super(...)` call provides.

---

## ⚠️ Limitations

- Because calling the parent's `__init__` is never enforced, forgetting `super().__init__(...)` is a genuinely common, easy-to-introduce bug that produces missing attributes rather than an obvious error.
- Multiple inheritance, while powerful, can create genuinely confusing method resolution scenarios when several parent classes define methods with the same name — the MRO (see [[Multiple Inheritance and the MRO]]) determines the outcome, but it isn't always intuitive at a glance.
- Deep or wide multiple-inheritance hierarchies can become difficult to reason about, especially for anyone unfamiliar with exactly how Python's MRO resolves conflicts.

---

## 🚨 Common Mistakes

- Forgetting to call `super().__init__(...)` in a subclass's `__init__`, silently leaving inherited attributes unset — a bug that often only surfaces later, when code tries to use an attribute that was never actually initialized.
- Assuming Python enforces a specific constructor call order the way Java does — it doesn't; `super().__init__(...)` can be called anywhere in the subclass's `__init__`, not necessarily first.
- Overusing multiple inheritance for convenience when a simpler composition-based design (an object *containing* another, rather than inheriting from it) would be clearer and less prone to MRO confusion.
- Confusing Python's `super()` with Java's `super` — Python's version takes no explicit arguments in modern usage and works through the MRO, not a single, fixed "the direct parent class."

---

## 📖 Further Reading

- Python documentation: "The Python Tutorial" — Section 9.5, "Inheritance"
- Python documentation: `super()` built-in function reference

---

## 💡 Wisdom from Mímir

Make calling `super().__init__(...)` the very first line of every subclass `__init__`, as a near-automatic habit — Python will never remind you it's missing, and the resulting bug (an attribute that mysteriously doesn't exist) can surface far from the actual cause, in a completely unrelated method that assumed the parent's setup had already happened.

---

## 🔗 Related Notes

- [[Classes and Objects in Python]]
- [[Multiple Inheritance and the MRO]]
- [[Abstract Base Classes]]
- [[Python Codex]]
