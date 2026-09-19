---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Python
difficulty: Intermediate
tags:
  - python-fundamentals
  - list-comprehensions
  - generators
aliases:
  - Comprehension Syntax Python
  - List Comprehension vs Generator Expression
publish: true
permalink: python/list-comprehensions-and-generator-expressions
---

# <span class="rune">ᛟ</span> List Comprehensions and Generator Expressions

> *Swap the brackets for parentheses, and a list comprehension that builds the whole thing up front becomes a generator expression that produces values one at a time, on demand — same shape, opposite philosophy about when work actually happens.*

---

## 🎯 Purpose

A list comprehension (`[expression for item in iterable if condition]`) builds an entire new list from an existing iterable in a single, compact, readable expression — the idiomatic Python replacement for the classic "create an empty list, loop, append" pattern. A generator expression uses nearly identical syntax with parentheses instead of brackets, producing values **lazily**, one at a time, without ever materializing the full result in memory at once.

---

## 🧠 Key Ideas

- List comprehension syntax: `[expr for item in iterable if condition]` — the `if` clause is optional and filters which items are included.
- List comprehensions build the **entire list immediately**, all at once, consuming memory proportional to the result size.
- Generator expressions use the same syntax with `()` instead of `[]`: `(expr for item in iterable if condition)` — they produce items **lazily**, one at a time, only when actually requested (via iteration or `next()`).
- Dict and set comprehensions extend the same pattern with `{}`: `{k: v for ...}` for dicts, `{expr for ...}` for sets — covered further in [[Dictionary and Set Comprehensions]].
- Nested loops are expressible directly in a comprehension (`[expr for x in outer for y in inner]`), reading left to right in the same order as the equivalent nested `for` loops would.

---

## ⚙️ How It Works

`[x * 2 for x in range(5)]` is functionally equivalent to building an empty list, looping through `range(5)`, and appending `x * 2` on each iteration — but expressed as a single, self-contained expression rather than several separate statements. The generator expression version, `(x * 2 for x in range(5))`, doesn't do any of that work upfront at all — it creates a generator object that computes and yields each value only when the caller actually asks for the next one, which means a generator expression over an enormous (or even infinite) sequence uses only a small, constant amount of memory, regardless of how many items it will eventually produce.

```text
[x * 2 for x in range(1_000_000)]   # builds ALL 1,000,000 values immediately — real memory cost NOW
(x * 2 for x in range(1_000_000))    # builds NOTHING yet — values computed one at a time, ONLY as requested
```

---

## 💻 Examples

```python
# The pattern comprehensions replace
squares = []
for x in range(10):
    squares.append(x ** 2)

# The idiomatic comprehension equivalent
squares = [x ** 2 for x in range(10)]

# With a filtering condition
evens = [x for x in range(20) if x % 2 == 0]

# Transform AND filter together
names = ["alice", "bob", "cara"]
capitalized_long = [name.upper() for name in names if len(name) > 3]

# Nested loops in a comprehension
pairs = [(x, y) for x in range(3) for y in range(2)]
# [(0,0), (0,1), (1,0), (1,1), (2,0), (2,1)]

# Generator expression — lazy, memory-efficient
gen = (x ** 2 for x in range(1_000_000))
print(next(gen))    # 0 — computed on demand
print(next(gen))     # 1 — the NEXT one, computed now

# Generators are commonly passed directly to functions expecting an iterable
total = sum(x ** 2 for x in range(1_000_000))   # no parentheses needed when it's the only argument

# A generator can only be consumed ONCE
gen2 = (x for x in range(3))
list(gen2)     # [0, 1, 2]
list(gen2)      # [] — already exhausted, nothing left
```

---

## 🚀 Real World Applications

- Replacing verbose "create empty list, loop, append" patterns with a single, readable comprehension
- Filtering and transforming a collection in one expression instead of two separate steps
- Using generator expressions for processing very large or unbounded data sources without loading everything into memory at once
- Passing a generator expression directly into aggregate functions (`sum()`, `max()`, `any()`, `all()`) without materializing an intermediate list at all

---

## ⚖️ Advantages

- Comprehensions are significantly more concise and often more readable than the equivalent multi-line loop-and-append pattern.
- Generator expressions provide genuine memory efficiency for large or infinite sequences, since nothing is computed until actually needed.
- Both integrate naturally with the rest of Python's iteration-based idioms (`for` loops, `sum()`, `any()`, unpacking).

---

## ⚠️ Limitations

- Deeply nested or heavily conditioned comprehensions can become genuinely harder to read than an equivalent explicit loop — readability should always take priority over compressing everything into one line.
- A generator can only be iterated **once** — once exhausted, it produces nothing further, and there's no way to "rewind" it without recreating it from scratch.
- List comprehensions still build the entire result in memory — for very large results, a generator expression (or a different approach entirely) is the more appropriate choice.

---

## 🚨 Common Mistakes

- Writing a comprehension so dense with nested loops and conditions that it becomes harder to understand than a straightforward multi-line loop would have been.
- Assuming a generator expression can be iterated more than once, then being confused when a second pass produces nothing.
- Building a full list comprehension when only a single pass through the data is actually needed, missing the memory-efficiency benefit a generator expression would have provided for free.
- Forgetting the parentheses around a generator expression are optional only when it's the sole argument to a function call — `sum(x for x in range(10))` works, but assigning a bare generator expression to a variable still requires explicit parentheses: `gen = (x for x in range(10))`.

---

## 📖 Further Reading

- Python documentation: "The Python Tutorial" — Section 5.1.3, "List Comprehensions"
- PEP 289: "Generator Expressions"

---

## 💡 Wisdom from Mímir

Reach for a list comprehension when the full result is genuinely needed all at once; reach for a generator expression when you're only ever going to iterate through it once, especially over something large. The syntax difference is one character (brackets vs. parentheses) — the actual decision is about whether materializing the entire result upfront is something you actually want to pay for.

---

## 🔗 Related Notes

- [[Dictionary and Set Comprehensions]]
- [[Iterators and Generators]]
- [[Control Flow - if, for, and while]]
- [[Python Codex]]
