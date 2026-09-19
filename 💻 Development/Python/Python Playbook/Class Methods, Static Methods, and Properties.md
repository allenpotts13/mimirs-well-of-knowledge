---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Python
difficulty: Intermediate
tags:
  - oop
  - classmethod
  - staticmethod
  - property
aliases:
  - "@classmethod vs @staticmethod"
  - Python Property Decorator
publish: true
permalink: python/class-methods-static-methods-and-properties
---

# <span class="rune">ᛟ</span> Class Methods, Static Methods, and Properties

> *A classmethod receives the class itself as its first argument, no matter which subclass actually called it — which is exactly the mechanism that makes a "named alternative constructor" possible at all.*

---

## 🎯 Purpose

Beyond ordinary instance methods (which take `self`), Python provides `@classmethod` (bound to the class rather than an instance, commonly used for alternative constructors), `@staticmethod` (bound to neither, essentially a plain function namespaced inside a class), and `@property` (lets a method be accessed like an attribute, without parentheses — Python's mechanism for controlled attribute access, replacing the getter/setter boilerplate common in Java).

---

## 🧠 Key Ideas

- `@classmethod` methods take `cls` (the class itself) as their first parameter instead of `self` — commonly used for **alternative constructors** that build an instance a different way (`Date.from_string(...)` alongside the normal `Date(...)`).
- `@staticmethod` methods take neither `self` nor `cls` — they're just ordinary functions that happen to live inside a class's namespace for organizational purposes, with no automatic access to instance or class state at all.
- `@property` turns a method into something accessed like a plain attribute (`obj.value`, no parentheses) — Python's idiomatic way to add getter logic (validation, computed values) without requiring callers to change how they access it.
- A property can also define a matching setter (`@value.setter`) to intercept assignment (`obj.value = 5`), providing the same validation capability as Java's setter pattern, but with attribute-style syntax at the call site.
- Because `@classmethod` receives the actual calling class (not a hardcoded reference to where the method was defined), it correctly respects subclassing — calling an inherited classmethod on a subclass passes that subclass as `cls`, not the original defining class.

---

## ⚙️ How It Works

`@classmethod` and `@staticmethod` are both descriptors that change how Python binds the method when it's accessed — a classmethod always receives the class (whichever one it was actually called through) as its first argument, while a staticmethod receives nothing automatic at all, behaving exactly like a standalone function that simply happens to be namespaced under the class. `@property` works by intercepting attribute *access* itself: `obj.value` doesn't directly look up a stored attribute named `value` — it calls the property's underlying getter method and returns whatever that method computes, making it indistinguishable at the call site from a plain stored attribute.

```text
class Circle:
    @classmethod
    def from_diameter(cls, diameter):    # cls = Circle (or a subclass, if called through one)
        return cls(diameter / 2)          # builds an instance using WHICHEVER class actually called this

    @staticmethod
    def is_valid_radius(r):                # no self, no cls — just a plain function, namespaced here
        return r > 0
```

---

## 💻 Examples

```python
class Circle:
    def __init__(self, radius):
        self._radius = radius

    @classmethod
    def from_diameter(cls, diameter):        # alternative constructor
        return cls(diameter / 2)              # cls(...) — respects subclasses correctly

    @staticmethod
    def is_valid_radius(radius):               # no self/cls needed — pure utility logic
        return radius > 0

    @property
    def radius(self):                            # accessed like an attribute: circle.radius
        return self._radius

    @radius.setter
    def radius(self, value):                       # intercepts: circle.radius = value
        if value <= 0:
            raise ValueError("Radius must be positive")
        self._radius = value

    @property
    def area(self):                                   # a COMPUTED, read-only property — no setter defined
        return 3.14159 * self._radius ** 2

c1 = Circle(5)
c2 = Circle.from_diameter(10)   # alternative constructor — c2's radius is 5.0

print(Circle.is_valid_radius(-5))   # False — called on the CLASS, no instance needed at all

print(c1.radius)      # 5 — looks like a plain attribute access, but actually calls the getter
c1.radius = 10          # calls the setter — validated
# c1.radius = -1          # raises ValueError — the setter's validation catches it
print(c1.area)            # 314.159 — computed fresh each time, no setter exists for this one

# Subclassing respects classmethod's cls correctly
class Sphere(Circle):
    pass

s = Sphere.from_diameter(10)   # cls = Sphere here, NOT Circle — returns an actual Sphere instance
print(type(s))                    # <class '__main__.Sphere'>
```

---

## 🚀 Real World Applications

- Providing named alternative constructors (`from_string()`, `from_dict()`, `from_diameter()`) alongside a class's normal `__init__`
- Grouping related utility functions inside a class namespace via `@staticmethod` when they conceptually belong there but don't need instance/class state
- Using `@property` to add validation to attribute assignment, or to expose a computed value, without forcing callers to use explicit `get_x()`/`set_x()` method calls
- Building a fluent, attribute-style public API while still retaining internal control over how values are actually stored and validated

---

## ⚖️ Advantages

- `@property` achieves the same validation/encapsulation benefits as Java's getter/setter pattern while keeping the call-site syntax as simple as plain attribute access.
- `@classmethod` alternative constructors provide clear, self-documenting ways to create instances from different kinds of input, all under one obviously-related family of names.
- `@staticmethod` cleanly organizes utility logic that conceptually belongs with a class without any unnecessary `self`/`cls` overhead.

---

## ⚠️ Limitations

- Overusing `@property` for what is really just a plain, unvalidated attribute adds unnecessary indirection without any real benefit.
- `@classmethod`'s behavior of receiving the actual calling subclass can be genuinely surprising if a method assumes it's always working with the base class it was originally defined in.
- Distinguishing when something should be a `@staticmethod` versus simply a standalone module-level function is sometimes a genuine judgment call with no universally agreed-upon answer.

---

## 🚨 Common Mistakes

- Defining a property setter's validation logic inconsistently with the constructor's own validation, allowing an object to be constructed in an invalid state even though later assignment through the property would have been correctly blocked.
- Using `@staticmethod` when `@classmethod` was actually needed (or vice versa) — a staticmethod that tries to construct "the current class" generically should almost always be a classmethod using `cls(...)` instead of hardcoding the class name directly.
- Forgetting a property with only a getter defined (no `@x.setter`) is effectively read-only — attempting `obj.x = value` raises `AttributeError: can't set attribute`.
- Adding `@property` to something that's genuinely just plain data with no validation or computation involved, adding indirection for no real benefit.

---

## 📖 Further Reading

- Python documentation: "Built-in Functions" — `classmethod()`, `staticmethod()`, `property()`
- Python documentation: "Descriptor HowTo Guide"

---

## 💡 Wisdom from Mímir

When writing an alternative constructor, always build it with `@classmethod` and `cls(...)`, never `@staticmethod` and a hardcoded class name — the difference is invisible until someone subclasses your class, at which point the hardcoded version quietly returns the wrong type while the classmethod version correctly returns an instance of whichever subclass actually called it.

---

## 🔗 Related Notes

- [[Classes and Objects in Python]]
- [[Encapsulation Conventions - Single and Double Underscore]]
- [[Abstract Base Classes]]
- [[Python Codex]]
