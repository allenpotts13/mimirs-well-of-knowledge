---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Python
difficulty: Intermediate
tags:
  - python-idioms
  - generators
  - yield
aliases:
  - yield keyword
  - "__iter__ and __next__"
publish: true
permalink: python/iterators-and-generators
---

# <span class="rune">ᛟ</span> Iterators and Generators

> *A `yield` doesn't return and end the function — it returns and PAUSES it, mid-execution, with every local variable exactly as it was, waiting to pick up on the very next line the moment someone asks for the next value.*

---

## 🎯 Purpose

Python's `for` loops work on anything **iterable**, and generators (functions using `yield` instead of `return`) are the easiest, most idiomatic way to create a custom iterator — producing a sequence of values lazily, one at a time, without ever building the entire sequence in memory. This is the actual mechanism underneath [[List Comprehensions and Generator Expressions|generator expressions]], `range()`, file reading, and much of Python's iteration machinery.

---

## 🧠 Key Ideas

- The **iterator protocol** requires two methods: `__iter__` (returns the iterator itself) and `__next__` (returns the next value, raising `StopIteration` when exhausted) — implementing both by hand is the "manual" way to build a custom iterable class.
- A **generator function** — any function containing at least one `yield` statement — automatically implements the entire iterator protocol for you, with none of that boilerplate.
- Calling a generator function doesn't run its body at all — it immediately returns a generator object; the function's actual code only starts executing on the first call to `next()` (or the first iteration of a `for` loop over it).
- Each `yield` pauses the function's execution, preserving all local state exactly as it was, and hands a value back to the caller — the very next call to `next()` resumes execution right after that `yield`, continuing until the next `yield` or the function's natural end.
- A generator can only be iterated through **once** — once exhausted (or once the function reaches its end), it's permanently done; a fresh call to the generator function is needed to iterate again from the start.

---

## ⚙️ How It Works

When Python compiles a function containing `yield` anywhere in its body, it treats the entire function specially — calling it doesn't execute any code immediately; it returns a generator object that remembers exactly where execution should begin. Each subsequent call to `next()` runs the function's code starting from wherever it last left off (initially, the very top) until it hits a `yield` statement, at which point execution pauses again, the yielded value is returned to the caller, and every local variable's exact state is preserved untouched until the next `next()` call resumes it.

```python
def count_up_to(n):
    i = 1
    while i <= n:
        yield i        # PAUSES here, returns i, remembers everything, waits
        i += 1          # resumes HERE on the next next() call

gen = count_up_to(3)   # NOTHING has run yet — just a generator object
next(gen)   # 1 — runs up to the first yield
next(gen)    # 2 — resumes right after the yield, runs to the NEXT yield
next(gen)     # 3
next(gen)      # StopIteration — the function reached its natural end
```

---

## 💻 Examples

```python
# A generator function
def count_up_to(n):
    i = 1
    while i <= n:
        yield i
        i += 1

for num in count_up_to(5):    # for loops handle StopIteration automatically
    print(num)                  # 1, 2, 3, 4, 5

# Manually driving a generator with next()
gen = count_up_to(3)
print(next(gen))   # 1
print(next(gen))    # 2
print(next(gen))     # 3
# print(next(gen))   # StopIteration — no more values

# Memory efficiency — generators process HUGE sequences without loading everything at once
def read_large_file(path):
    with open(path) as f:
        for line in f:
            yield line.strip()    # ONE line in memory at a time, never the whole file

# A custom iterator class, the "manual" way (what generators do automatically)
class CountUpTo:
    def __init__(self, n):
        self.n = n
        self.current = 1

    def __iter__(self):
        return self

    def __next__(self):
        if self.current > self.n:
            raise StopIteration
        value = self.current
        self.current += 1
        return value

for num in CountUpTo(3):    # works identically to the generator version above
    print(num)

# Generators can receive values back too, via .send() (advanced, less common)
def echo():
    while True:
        received = yield
        print(f"Received: {received}")

e = echo()
next(e)          # prime the generator — advances to the first yield
e.send("hello")   # "Received: hello"

# infinite generators — only safe BECAUSE they're lazy
def infinite_counter():
    i = 0
    while True:
        yield i
        i += 1

counter = infinite_counter()
print(next(counter))   # 0
print(next(counter))    # 1  — could go on forever, but only computes what's actually asked for
```

---

## 🚀 Real World Applications

- Processing very large files or datasets line by line without loading the entire thing into memory at once
- Building custom, lazily-evaluated sequences (an infinite counter, a Fibonacci generator) that would be impossible to represent as a complete, materialized list
- Implementing custom iteration behavior for a domain-specific class, using either the generator shortcut or the full manual `__iter__`/`__next__` protocol
- Chaining generators together to build efficient, memory-conscious data-processing pipelines

---

## ⚖️ Advantages

- Generators provide the memory efficiency of lazy evaluation with far less code than manually implementing the full iterator protocol.
- The `yield` keyword makes writing a custom iterator nearly as simple as writing a normal function with a loop, hiding all the `__iter__`/`__next__`/`StopIteration` machinery.
- Generators naturally support infinite or unbounded sequences, since values are only ever computed on demand, never all at once upfront.

---

## ⚠️ Limitations

- A generator can only be iterated through once — there's no way to "reset" or "rewind" it; a fresh call to the generator function is required to start over.
- Debugging a paused generator's exact internal state can be less intuitive than debugging a straightforward function, especially across multiple `yield` points.
- Generators that are never fully exhausted (an infinite generator processed in a loop with a `break`, for instance) can hold onto resources if not properly closed — `.close()` exists for this, though it's rarely needed explicitly in simple `for`-loop usage.

---

## 🚨 Common Mistakes

- Trying to iterate a generator a second time, expecting the same sequence again, and getting nothing — a new generator object must be created by calling the generator function again.
- Assuming a generator function runs immediately when called — it doesn't; nothing executes until the first `next()` call (or the first iteration in a `for` loop) actually happens.
- Building an unnecessarily large intermediate list when a generator (or generator expression) would have accomplished the same processing far more memory-efficiently.
- Forgetting `yield` pauses and preserves state exactly, and assuming local variables reset between `next()` calls the way they would in an ordinary function called repeatedly from scratch.

---

## 📖 Further Reading

- Python documentation: "The Python Tutorial" — Section 9.10, "Generators" and 9.11, "Generator Expressions"
- PEP 255: "Simple Generators"

---

## 💡 Wisdom from Mímir

Whenever a custom class exists purely to support iteration — implementing `__iter__` and `__next__` by hand, tracking state manually — stop and ask whether a plain generator function with `yield` would do the exact same job in a fraction of the code. The manual iterator protocol is worth knowing conceptually, but `yield` is almost always the more Pythonic, more maintainable choice in practice.

---

## 🔗 Related Notes

- [[List Comprehensions and Generator Expressions]]
- [[Control Flow - if, for, and while]]
- [[Context Managers and the with Statement]]
- [[Python Codex]]
