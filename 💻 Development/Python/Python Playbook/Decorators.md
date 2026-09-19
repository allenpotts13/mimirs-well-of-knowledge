---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Python
difficulty: Advanced
tags:
  - python-idioms
  - decorators
  - higher-order-functions
aliases:
  - "@decorator syntax"
  - functools.wraps
publish: true
permalink: python/decorators
---

# <span class="rune">ᛟ</span> Decorators

> *`@my_decorator` above a function isn't special syntax invoking some separate mechanism — it's exactly, character for character, equivalent to writing `func = my_decorator(func)` right below the original definition. The `@` symbol is just a shortcut for that one line.*

---

## 🎯 Purpose

A decorator is a function that takes another function (or class) as input and returns a modified or wrapped version of it — used to add behavior (logging, timing, access control, caching) to a function without changing its actual internal code. The `@decorator_name` syntax above a function definition is pure syntactic sugar for calling the decorator function manually and reassigning the result.

---

## 🧠 Key Ideas

- A decorator is just a function that accepts a function as its argument and returns a (usually wrapped) function in its place.
- `@my_decorator` written above `def my_function():` is exactly equivalent to writing `my_function = my_decorator(my_function)` immediately after the original definition — nothing more mysterious than that.
- The typical decorator pattern defines an inner `wrapper` function (using [[Args and Kwargs|`*args`/`**kwargs`]] to accept any signature) that calls the original function and can run code before and/or after it.
- `functools.wraps` should be applied to the inner `wrapper` function inside every decorator — without it, the decorated function loses its original `__name__`, docstring, and other metadata, which can break debugging, documentation tools, and introspection.
- Decorators can accept their own arguments by adding an extra layer of nesting — a "decorator factory" that returns the actual decorator, which then returns the wrapper.

---

## ⚙️ How It Works

When Python parses `@logger` immediately above `def greet(): ...`, it doesn't do anything exotic — it defines `greet` normally, then immediately calls `logger(greet)`, and rebinds the name `greet` to whatever that call returns. Since `logger` typically returns a new `wrapper` function that internally calls the original `greet` (often before/after doing something else), every subsequent call to `greet()` actually invokes `wrapper()`, which in turn calls the real, original function — the original is still fully there, just now wrapped by an extra layer of behavior.

```text
@logger
def greet():
    print("Hello")

# is EXACTLY equivalent to:

def greet():
    print("Hello")
greet = logger(greet)     # greet is now REBOUND to whatever logger() returned
```

---

## 💻 Examples

```python
import functools
import time

# A basic decorator
def logger(func):
    @functools.wraps(func)          # preserves func's __name__, docstring, etc. — ALWAYS include this
    def wrapper(*args, **kwargs):    # accepts ANY signature, forwards everything through
        print(f"Calling {func.__name__}")
        result = func(*args, **kwargs)
        print(f"{func.__name__} finished")
        return result
    return wrapper

@logger
def greet(name):
    print(f"Hello, {name}!")

greet("Alice")
# Calling greet
# Hello, Alice!
# greet finished

print(greet.__name__)   # "greet" — CORRECT, thanks to functools.wraps (without it, this would be "wrapper")

# A timing decorator — a very common real-world use case
def timer(func):
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        start = time.perf_counter()
        result = func(*args, **kwargs)
        elapsed = time.perf_counter() - start
        print(f"{func.__name__} took {elapsed:.4f}s")
        return result
    return wrapper

@timer
def slow_function():
    time.sleep(1)

slow_function()   # "slow_function took 1.0001s"

# A decorator that ACCEPTS ITS OWN arguments — an extra layer of nesting
def repeat(times):
    def decorator(func):
        @functools.wraps(func)
        def wrapper(*args, **kwargs):
            for _ in range(times):
                func(*args, **kwargs)
        return wrapper
    return decorator

@repeat(times=3)
def say_hi():
    print("Hi!")

say_hi()   # prints "Hi!" three times

# Stacking multiple decorators — applied BOTTOM UP
@logger
@timer
def process():
    pass
# equivalent to: process = logger(timer(process))
```

---

## 🚀 Real World Applications

- Adding logging, timing, or performance profiling to functions without modifying their internal implementation at all
- Implementing caching (`functools.lru_cache` is itself a built-in decorator) to memoize expensive function calls
- Enforcing access control or authentication checks around web framework route handlers (a very common pattern in Flask/Django-style frameworks)
- Retry logic, rate limiting, and input validation applied uniformly across many functions via a single reusable decorator

---

## ⚖️ Advantages

- Cleanly separates cross-cutting concerns (logging, timing, caching) from a function's actual core logic, without needing to modify that logic directly.
- A single decorator can be reused across many unrelated functions, avoiding repeated boilerplate.
- The `@` syntax reads clearly at the definition site, making it immediately visible that a function has been augmented with extra behavior.

---

## ⚠️ Limitations

- Forgetting `functools.wraps` silently corrupts the decorated function's metadata (`__name__`, `__doc__`), which can break debugging tools, documentation generators, and any code that introspects the function by name.
- Stacking multiple decorators can make it genuinely harder to trace exactly what order operations happen in, especially for anyone unfamiliar with the "applied bottom-up" rule.
- Decorators that swallow exceptions or silently alter return values can make debugging significantly harder if not carefully and transparently designed.

---

## 🚨 Common Mistakes

- Omitting `@functools.wraps(func)` on the inner wrapper function, silently losing the original function's name and docstring — a subtle bug that often isn't noticed until some other tool (a debugger, a test framework, `help()`) reports the wrong function name.
- Forgetting the wrapper function must accept and forward `*args`/`**kwargs` to support decorating functions with any signature, not just the one the decorator author happened to test with.
- Misunderstanding decorator stacking order — decorators apply bottom-up (the one closest to the function runs first, wrapping innermost), which is easy to get backwards when reasoning about a stack of several.
- Forgetting a decorator that accepts its own arguments (like `@repeat(times=3)`) needs an extra layer of function nesting compared to a plain, argument-less decorator.

---

## 📖 Further Reading

- Python documentation: `functools` — especially `functools.wraps` and `functools.lru_cache`
- Python documentation: "The Python Tutorial" — Section 7.6, "Decorators" (via the broader function definitions section)

---

## 💡 Wisdom from Mímir

Never write a decorator's inner `wrapper` function without `@functools.wraps(func)` immediately above it — treat this as a fixed, non-negotiable pair, the same discipline as always chaining an exception's original cause. The cost is one line; the alternative is a decorated function that lies about its own name to every tool that ever inspects it.

---

## 🔗 Related Notes

- [[Args and Kwargs]]
- [[Lambda Functions in Python]]
- [[Context Managers and the with Statement]]
- [[Python Codex]]
