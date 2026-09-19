---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Python
difficulty: Intermediate
tags:
  - python-idioms
  - comprehensions
  - dict-comprehension
aliases:
  - Dict Comprehension Python
  - Set Comprehension Python
publish: true
permalink: python/dictionary-and-set-comprehensions
---

# <span class="rune">ᛟ</span> Dictionary and Set Comprehensions

> *One curly brace, two completely different structures — a colon inside makes it a dict comprehension, its absence makes it a set comprehension, and mixing that up is an easy way to build the wrong thing entirely.*

---

## 🎯 Purpose

Extending the same comprehension pattern from [[List Comprehensions and Generator Expressions]], Python also supports **dict comprehensions** (`{key_expr: value_expr for item in iterable}`) and **set comprehensions** (`{expr for item in iterable}`) — both using curly braces, distinguished only by whether a colon separates a key and value inside.

---

## 🧠 Key Ideas

- Dict comprehension syntax: `{key: value for item in iterable if condition}` — builds a dictionary in one expression, exactly mirroring a list comprehension's shape but with a `key: value` pair instead of a single expression.
- Set comprehension syntax: `{expr for item in iterable if condition}` — identical to a dict comprehension's braces, but without the colon, producing a `set` instead.
- An empty `{}` is **always a dict**, never an empty set — Python resolves this ambiguity in favor of dict; `set()` (the constructor) is required to create an empty set.
- Both forms support the same filtering (`if condition`) and nested-loop capabilities as list comprehensions.
- Dict comprehensions are extremely common for **inverting** a dictionary (swapping keys and values) or building a lookup table from two parallel sequences.

---

## ⚙️ How It Works

A dict comprehension is functionally equivalent to building an empty dict, looping through the iterable, and assigning `result[key_expr] = value_expr` on each iteration — just expressed as one self-contained expression instead of several statements. A set comprehension follows the identical logic as a list comprehension, except the accumulating collection is a `set` (which automatically discards duplicates) rather than a `list` that preserves every value including duplicates.

```text
{k: v for k, v in pairs}          # dict comprehension — colon present
{x for x in items}                  # set comprehension — no colon, duplicates auto-removed

{}                                    # ALWAYS an empty dict — never an empty set
set()                                  # the only way to get a genuinely empty set
```

---

## 💻 Examples

```python
# Dict comprehension — building from an iterable
names = ["Alice", "Bob", "Cara"]
name_lengths = {name: len(name) for name in names}
print(name_lengths)   # {'Alice': 5, 'Bob': 3, 'Cara': 4}

# Dict comprehension with a filter
scores = {"Alice": 85, "Bob": 45, "Cara": 92}
passing = {name: score for name, score in scores.items() if score >= 60}
print(passing)   # {'Alice': 85, 'Cara': 92}

# Inverting a dictionary — a very common real-world use
original = {"a": 1, "b": 2, "c": 3}
inverted = {v: k for k, v in original.items()}
print(inverted)   # {1: 'a', 2: 'b', 3: 'c'}

# Set comprehension — automatic deduplication
words = ["apple", "banana", "apple", "cherry"]
unique_lengths = {len(w) for w in words}
print(unique_lengths)   # {5, 6} — duplicates automatically collapsed (apple appears twice, once counted)

# Set comprehension with a condition
numbers = [1, 2, 3, 4, 5, 6, 7, 8]
even_squares = {n ** 2 for n in numbers if n % 2 == 0}
print(even_squares)   # {4, 16, 36, 64}

# The {} ambiguity — always a dict, never a set
empty = {}
print(type(empty))     # <class 'dict'> — NOT set, even though it "looks" like it could be either
empty_set = set()        # the ONLY way to create a genuinely empty set

# Building a lookup dict from two parallel lists
keys = ["a", "b", "c"]
values = [1, 2, 3]
lookup = {k: v for k, v in zip(keys, values)}
print(lookup)   # {'a': 1, 'b': 2, 'c': 3}
```

---

## 🚀 Real World Applications

- Inverting a dictionary (swapping keys and values) in a single, readable expression
- Building a fast lookup table from two parallel sequences using `zip()` combined with a dict comprehension
- Deduplicating a transformed collection while simultaneously filtering it, in one set comprehension
- Filtering a dictionary down to only the entries matching a specific condition, without a separate loop-and-build step

---

## ⚖️ Advantages

- Dramatically more concise than the equivalent multi-line loop for building a dict or set from an existing iterable.
- Reads naturally once the pattern is familiar, closely mirroring the already-established list comprehension syntax from [[List Comprehensions and Generator Expressions]].
- Set comprehensions provide automatic, built-in deduplication as a natural side effect of the underlying data structure, with no extra logic required.

---

## ⚠️ Limitations

- The visual similarity between dict and set comprehensions (both use `{}`) means a missing or extra colon is an easy typo that changes the resulting structure entirely, sometimes without an obvious error.
- Inverting a dictionary via comprehension silently discards duplicate values if the original dict has multiple keys sharing the same value — only the last one encountered survives in the inverted result.
- Overly dense comprehensions with multiple conditions or nested loops can become harder to read than a straightforward explicit loop, exactly as with list comprehensions.

---

## 🚨 Common Mistakes

- Writing `{x for x in items}` when a dict was actually intended, forgetting the colon needed to make it a dict comprehension instead of a set comprehension.
- Assuming `{}` creates an empty set — it always creates an empty dict; `set()` is required for a genuinely empty set.
- Inverting a dictionary with duplicate values via a comprehension and being surprised some original keys are missing from the result — only the last-seen key for each duplicate value survives.
- Building a set comprehension when a list comprehension was actually needed, losing both ordering and duplicate values unexpectedly.

---

## 📖 Further Reading

- Python documentation: "The Python Tutorial" — Section 5.1.4, "Nested List Comprehensions" (dict/set comprehension coverage is nearby in the broader data structures section)
- Python documentation: `dict` and `set` — Mapping and Set Types

---

## 💡 Wisdom from Mímir

Before inverting a dictionary with a comprehension, check whether the original values are actually guaranteed unique — if they aren't, the "inverted" result will silently drop keys, keeping only the last one seen for each duplicate value, with no warning that anything was lost.

---

## 🔗 Related Notes

- [[List Comprehensions and Generator Expressions]]
- [[Dictionaries]]
- [[Lists, Tuples, and Sets]]
- [[Python Codex]]
