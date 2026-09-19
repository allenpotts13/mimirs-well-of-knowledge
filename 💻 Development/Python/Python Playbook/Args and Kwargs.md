---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Python
difficulty: Intermediate
tags:
  - python-fundamentals
  - args-kwargs
  - unpacking
aliases:
  - "*args and **kwargs"
  - Variadic Arguments Python
publish: true
permalink: python/args-and-kwargs
---

# <span class="rune">ᛟ</span> Args and Kwargs

> *`*args` and `**kwargs` aren't magic keywords — `*` and `**` are the actual mechanism, and `args`/`kwargs` are just conventional names anyone could rename without changing a thing.*

---

## 🎯 Purpose

`*args` and `**kwargs` let a function accept an arbitrary number of positional or keyword arguments, respectively — essential for writing flexible functions that don't know in advance how many arguments they'll receive, and for wrapper functions (especially decorators) that need to pass arguments through to another function without knowing its exact signature.

---

## 🧠 Key Ideas

- `*args` collects any extra **positional** arguments into a `tuple` inside the function — the name `args` is convention only; the actual mechanism is the single `*` prefix.
- `**kwargs` collects any extra **keyword** arguments into a `dict` inside the function — again, `kwargs` is just convention; `**` is the actual operator.
- The same `*`/`**` syntax works in reverse at a **call site** to unpack an existing collection into separate arguments: `func(*my_list)` spreads a list's items as positional arguments; `func(**my_dict)` spreads a dict's items as keyword arguments.
- Parameter order in a function signature is fixed: regular positional parameters, then `*args`, then keyword-only parameters, then `**kwargs`.
- This is the mechanism that makes generic wrapper functions (most importantly, [[Decorators]]) possible — a wrapper can accept `(*args, **kwargs)` and forward them unchanged to whatever function it's wrapping, regardless of that function's actual signature.

---

## ⚙️ How It Works

When a function is defined with `*args`, any positional arguments beyond the explicitly named parameters are collected into a tuple bound to that name; `**kwargs` does the same for keyword arguments, collecting them into a dict. The reverse operation — unpacking — uses identical syntax at the call site: prefixing an existing list/tuple with `*` spreads its elements as separate positional arguments, and prefixing a dict with `**` spreads its key-value pairs as separate keyword arguments, effectively doing the opposite of collection.

```text
def f(*args, **kwargs):
    print(args)     # a TUPLE of every extra positional argument
    print(kwargs)   # a DICT of every extra keyword argument

f(1, 2, 3, name="Alice", age=30)
# args   = (1, 2, 3)
# kwargs = {'name': 'Alice', 'age': 30}
```

---

## 💻 Examples

```python
# Collecting arbitrary positional arguments
def total(*args):
    return sum(args)

total(1, 2, 3)         # 6
total(1, 2, 3, 4, 5)    # 15 — works with ANY number of arguments

# Collecting arbitrary keyword arguments
def describe(**kwargs):
    for key, value in kwargs.items():
        print(f"{key}: {value}")

describe(name="Alice", age=30, city="NYC")

# Combined — the classic full flexible signature
def flexible(required, *args, **kwargs):
    print("required:", required)
    print("args:", args)
    print("kwargs:", kwargs)

flexible(1, 2, 3, name="Alice")
# required: 1
# args: (2, 3)
# kwargs: {'name': 'Alice'}

# UNPACKING — the reverse direction, at the CALL site
def add(a, b, c):
    return a + b + c

numbers = [1, 2, 3]
add(*numbers)              # unpacks the list into THREE separate positional arguments — same as add(1, 2, 3)

data = {"a": 1, "b": 2, "c": 3}
add(**data)                 # unpacks the dict into keyword arguments — same as add(a=1, b=2, c=3)

# A decorator using *args/**kwargs to wrap ANY function signature
def logger(func):
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__} with {args}, {kwargs}")
        return func(*args, **kwargs)   # forwards EVERYTHING through, unchanged
    return wrapper

@logger
def multiply(a, b):
    return a * b

multiply(3, 4)   # logs the call, then still returns 12
```

---

## 🚀 Real World Applications

- Writing decorators that need to wrap functions with unknown, arbitrary signatures (see [[Decorators]])
- Building flexible utility functions (`print()`-like functions, aggregation functions) that accept a variable number of arguments
- Unpacking a list or dict directly into a function call instead of manually extracting individual values
- Subclass constructors that accept and forward arbitrary arguments to a parent class's `__init__` via `super().__init__(*args, **kwargs)`

---

## ⚖️ Advantages

- Enables genuinely flexible function signatures without needing to predict every possible call pattern in advance.
- Makes generic wrapper/decorator functions possible regardless of the wrapped function's specific parameter list.
- Unpacking at the call site avoids verbose manual argument extraction from an existing collection.

---

## ⚠️ Limitations

- Overusing `*args`/`**kwargs` in ordinary (non-wrapper) function signatures can obscure exactly what arguments a function actually expects, hurting readability and IDE autocomplete support.
- Debugging a function that accepts `**kwargs` can be harder, since the actual expected keys aren't visible in the signature itself — they're often only documented in a docstring, if at all.
- Forwarding `*args`/`**kwargs` through several layers of wrapper functions can make it genuinely difficult to trace what arguments ultimately reach the innermost function.

---

## 🚨 Common Mistakes

- Assuming `args` and `kwargs` are special reserved keywords — they're just conventional variable names; `*values`, `**options` work identically, since `*`/`**` are the actual mechanism.
- Forgetting the required parameter order in a signature (positional, then `*args`, then keyword-only, then `**kwargs`), causing a `SyntaxError`.
- Using `*args`/`**kwargs` for a function whose parameters are actually well-known and fixed, sacrificing clarity for flexibility that was never actually needed.
- Forgetting to forward both `*args` AND `**kwargs` in a wrapper function, silently dropping whichever one was omitted for any wrapped function that actually relies on it.

---

## 📖 Further Reading

- Python documentation: "The Python Tutorial" — Section 4.7.3, "Arbitrary Argument Lists" and 4.7.4, "Unpacking Argument Lists"

---

## 💡 Wisdom from Mímir

Reach for `*args`/`**kwargs` specifically when a function's job is to forward arguments somewhere else without caring what they are — decorators and generic wrappers are the textbook case. For an ordinary function with genuinely known parameters, naming them explicitly is almost always the more readable, more maintainable choice.

---

## 🔗 Related Notes

- [[Functions and Default Arguments]]
- [[Decorators]]
- [[Inheritance and super()]]
- [[Python Codex]]
