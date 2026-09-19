---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Python
difficulty: Beginner
tags:
  - oop
  - classes
  - self-parameter
aliases:
  - Python self
  - __init__ Method
publish: true
permalink: python/classes-and-objects-in-python
---

# <span class="rune">ᛟ</span> Classes and Objects in Python

> *`self` isn't a keyword, isn't magic, and isn't optional in the parameter list — it's just the first parameter Python automatically hands you when a method is called on an instance, and any name would technically work in its place.*

---

## 🎯 Purpose

Python classes define blueprints for objects, following the same conceptual "blueprint vs. instance" model as Java, but with a distinctly Python mechanism for how instance methods access their own object's data: the explicit `self` parameter. Understanding `self` — that it's just a regular parameter, always passed automatically as the first argument to instance methods — resolves a huge share of early Python OOP confusion.

---

## 🧠 Key Ideas

- A class is defined with `class ClassName:`; `__init__` is the initializer method (analogous to a Java constructor), automatically called when a new instance is created.
- Every instance method's **first parameter must be `self`**, representing the specific instance the method was called on — Python passes it automatically; you never supply it yourself at the call site (`my_dog.bark()`, not `my_dog.bark(my_dog)`).
- Instance attributes are typically set inside `__init__` via `self.attribute_name = value` — there's no separate "field declaration" section the way Java requires; attributes simply come into existence the moment they're assigned.
- Python has no strict access modifiers (`public`/`private`/`protected`) enforced by the language — visibility is governed entirely by convention, covered in depth in [[Encapsulation Conventions - Single and Double Underscore]].
- `type(instance)` or `instance.__class__` reveals an object's actual class; `isinstance(instance, ClassName)` is the idiomatic way to check whether an object belongs to a given class (or its subclasses).

---

## ⚙️ How It Works

When `my_dog.bark()` is called, Python actually translates this internally into `Dog.bark(my_dog)` — the object the method was called *on* is automatically passed as the first argument, which is exactly why every instance method's signature must explicitly declare a parameter (conventionally named `self`) to receive it. `__init__` follows the same rule: `Dog("Rex")` calls `Dog.__init__(new_instance, "Rex")` behind the scenes, with the newly created (but still empty) instance automatically supplied as `self`, ready to have its attributes set.

```text
class Dog:
    def __init__(self, name):    # self = the instance being created, "name" = the argument passed in
        self.name = name          # attaches "name" as an attribute ON this specific instance

    def bark(self):                # self = whichever instance actually called .bark()
        print(f"{self.name} says woof!")

rex = Dog("Rex")     # Python calls: Dog.__init__(rex, "Rex")
rex.bark()             # Python calls: Dog.bark(rex)  →  "Rex says woof!"
```

---

## 💻 Examples

```python
class Car:
    def __init__(self, make, model, year):
        self.make = make          # instance attributes — each Car gets its OWN copy
        self.model = model
        self.year = year
        self.mileage = 0           # a sensible default, not passed in

    def drive(self, miles):
        self.mileage += miles

    def __str__(self):              # controls how print(car) displays the object
        return f"{self.year} {self.make} {self.model} ({self.mileage} mi)"

car1 = Car("Toyota", "Corolla", 2022)
car2 = Car("Honda", "Civic", 2023)

car1.drive(100)
car2.drive(50)

print(car1)   # 2022 Toyota Corolla (100 mi) — via __str__
print(car2)   # 2023 Honda Civic (50 mi) — independent state, same class

# Checking type
print(isinstance(car1, Car))   # True
print(type(car1))               # <class '__main__.Car'>

# Class-level (shared) attribute vs instance attribute
class Counter:
    total_created = 0            # SHARED across every instance — a class attribute

    def __init__(self):
        Counter.total_created += 1
        self.id = Counter.total_created   # unique PER instance

a = Counter()
b = Counter()
print(Counter.total_created)   # 2 — shared, incremented by both
print(a.id, b.id)                # 1 2 — each instance's own value
```

---

## 🚀 Real World Applications

- Modeling any real-world entity with its own data and behavior (`User`, `Order`, `Product`), exactly as in any OOP language
- Using `__init__` to enforce that an object is fully, correctly initialized the moment it's created
- Overriding `__str__` (and `__repr__`) to make objects print in a genuinely useful, readable way — the Python equivalent of Java's `toString()` override
- Distinguishing class-level attributes (shared, like Java's `static` fields) from instance attributes (per-object) when a value should or shouldn't be shared across every instance

---

## ⚖️ Advantages

- No boilerplate field declarations — attributes are created simply by assigning to them, keeping class definitions concise.
- `self` being an ordinary, explicit parameter (rather than an implicit hidden mechanism) makes it very clear, once understood, exactly how instance methods access their own data.
- Dunder methods like `__str__`/`__repr__`/`__eq__` (covered in [[Dunder Methods - __init__, __str__, __repr__, and __eq__]]) let a class integrate naturally with Python's built-in functions and operators.

---

## ⚠️ Limitations

- The complete absence of enforced access modifiers means nothing technically prevents external code from directly modifying an attribute that was only ever meant to be internal — discipline and convention (not the language) are what protect intended encapsulation.
- Forgetting `self` as the first parameter in an instance method definition, or accidentally omitting it when defining a method, produces confusing `TypeError`s about argument counts that don't obviously point to the real cause.
- Attributes created dynamically (simply by assigning `self.new_attr = value` anywhere, even outside `__init__`) can make it harder to know a class's full set of attributes just by reading its definition.

---

## 🚨 Common Mistakes

- Forgetting to include `self` as the first parameter in an instance method definition, causing a `TypeError: method() takes 0 positional arguments but 1 was given` when it's actually called.
- Setting an attribute directly on the class (`Counter.total_created`) when an instance-specific attribute was intended, accidentally sharing a value across every instance.
- Assuming `self` is a reserved keyword — it's purely a naming convention; the language would accept any name in that position, but deviating from `self` would confuse literally every other Python developer reading the code.
- Defining attributes inconsistently — sometimes in `__init__`, sometimes added later in other methods — making it hard to know a class's full attribute set without reading every method.

---

## 📖 Further Reading

- Python documentation: "The Python Tutorial" — Section 9, "Classes"

---

## 💡 Wisdom from Mímir

The moment `self` stops feeling like magic is the moment you mentally rewrite `my_dog.bark()` as `Dog.bark(my_dog)` — every instance method call in Python is really just a regular function call with the instance automatically slipped in as the first argument, and internalizing that removes nearly all of the initial confusion around `self`.

---

## 🔗 Related Notes

- [[Inheritance and super()]]
- [[Dunder Methods - __init__, __str__, __repr__, and __eq__]]
- [[Encapsulation Conventions - Single and Double Underscore]]
- [[Python Codex]]
