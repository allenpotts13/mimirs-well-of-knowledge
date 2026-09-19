---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Python
difficulty: Beginner
tags:
  - python-fundamentals
  - functions
  - mutable-default-argument
aliases:
  - Mutable Default Argument Trap
  - def keyword
publish: true
permalink: python/functions-and-default-arguments
---

# <span class="rune">ᛟ</span> Functions and Default Arguments

> *A default argument value is evaluated exactly once, at the moment the function is defined — not once per call. That single fact is the entire explanation behind Python's most infamous beginner trap.*

---

## 🎯 Purpose

Functions in Python are defined with `def`, support default parameter values, and can be called with positional or keyword arguments interchangeably. The mechanics look straightforward, but one specific detail — *when* default argument values are actually evaluated — creates one of the most well-known and easy-to-fall-into bugs in the entire language, especially for anyone using a mutable object (a list or dict) as a default.

---

## 🧠 Key Ideas

- `def function_name(param1, param2=default_value):` defines a function with one required and one optional (defaulted) parameter — defaulted parameters must come *after* required ones.
- Arguments can be passed **positionally** (matched by order) or as **keyword arguments** (matched by name, `function_name(param2=value)`), and the two styles can be mixed as long as positional arguments come first.
- Default argument values are evaluated **exactly once**, when the `def` statement itself executes (i.e., when the function is defined) — **not** freshly on every call.
- This means a **mutable default argument** (a list, dict, or set) is shared across every call that doesn't explicitly override it — modifications from one call persist and leak into subsequent calls, a famous, specific pitfall.
- Functions are themselves objects in Python — they can be assigned to variables, passed as arguments, and returned from other functions, the same first-class-citizen treatment covered further in [[Lambda Functions in Python]] and [[Decorators]].

---

## ⚙️ How It Works

When Python executes `def greet(name, items=[]):`, the empty list `[]` is created **once**, at that moment, and becomes permanently attached to the function object as its default value for `items` — every subsequent call to `greet()` that doesn't supply its own `items` argument receives that *exact same* list object, not a fresh empty one. If the function body mutates that list (`items.append(...)`), the mutation persists on the shared default object, silently affecting every future call that also relies on the default.

```text
def add_item(item, basket=[]):     # basket=[] created ONCE, at definition time
    basket.append(item)
    return basket

add_item("apple")   # returns ["apple"] — basket is the ONE shared default list, now holds "apple"
add_item("banana")  # returns ["apple", "banana"] — SAME list, "apple" is STILL there!
```

---

## 💻 Examples

```python
def greet(name, greeting="Hello"):
    return f"{greeting}, {name}!"

greet("Alice")                    # "Hello, Alice!"
greet("Bob", "Hi")                 # "Hi, Bob!"
greet(name="Cara", greeting="Hey")  # keyword arguments — order doesn't matter
greet(greeting="Yo", name="Dee")     # ALSO fine

# THE TRAP — mutable default argument
def add_item(item, basket=[]):
    basket.append(item)
    return basket

print(add_item("apple"))    # ['apple']
print(add_item("banana"))   # ['apple', 'banana'] — SURPRISE — the previous call's data is still there

# THE FIX — use None as a sentinel, create the mutable default fresh INSIDE the function
def add_item_fixed(item, basket=None):
    if basket is None:
        basket = []            # a NEW list, created fresh on EVERY call
    basket.append(item)
    return basket

print(add_item_fixed("apple"))    # ['apple']
print(add_item_fixed("banana"))   # ['banana'] — correctly independent

# Functions are first-class objects
def square(x): return x * x
operation = square           # assign the function itself, no () — not calling it
print(operation(5))           # 25
```

---

## 🚀 Real World Applications

- Using keyword arguments to make function calls self-documenting, especially for functions with several optional parameters
- Recognizing and avoiding the mutable default argument trap whenever a function parameter's default is a list, dict, or set
- Passing functions as values (to `sorted(key=...)`, `map()`, `filter()`, or as callback parameters) since functions are ordinary first-class objects
- Designing function signatures with sensible defaults to reduce boilerplate at call sites while keeping the function flexible

---

## ⚖️ Advantages

- Default arguments reduce boilerplate at call sites for the common case, while still allowing full customization when needed.
- Keyword arguments make call sites self-documenting and let arguments be supplied in any order.
- Functions being first-class objects enables clean functional-style patterns (passing behavior as a value) without special syntax.

---

## ⚠️ Limitations

- The mutable default argument behavior is a genuine language design quirk that trips up nearly every Python developer at least once, and requires a specific, deliberate workaround (the `None` sentinel pattern) rather than an obvious fix.
- Mixing many positional and keyword arguments in complex function signatures can occasionally reduce readability rather than improve it if overused.

---

## 🚨 Common Mistakes

- Using a mutable object (`[]`, `{}`, `set()`) as a default argument value directly, unaware that it's shared and persists across calls — always use `None` as the sentinel and create the actual mutable default fresh inside the function body instead.
- Placing a required parameter after a defaulted one (`def f(a=1, b):`), which is a `SyntaxError` — defaulted parameters must always come after required ones.
- Forgetting keyword-only arguments require positional arguments to come first when mixing both styles in a single call.
- Confusing `operation = square` (assigning the function object itself) with `operation = square()` (calling it and assigning its *return value*) — a very easy typo with very different results.

---

## 📖 Further Reading

- Python documentation: "The Python Tutorial" — Section 4.7, "More on Defining Functions"
- Python documentation: "Default Argument Values" (explicitly documents the once-at-definition-time evaluation)

---

## 💡 Wisdom from Mímir

Never use a mutable literal (`[]`, `{}`) as a default argument value — full stop, no exceptions. Use `None` and create the real default fresh inside the function body every time. This isn't a stylistic preference; it's the single most reliable way to avoid one of Python's most famous and most frequently rediscovered bugs.

---

## 🔗 Related Notes

- [[Args and Kwargs]]
- [[Lambda Functions in Python]]
- [[Lists, Tuples, and Sets]]
- [[Python Codex]]
