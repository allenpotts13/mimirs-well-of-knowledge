---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Python
difficulty: Intermediate
tags:
  - standard-library
  - collections-module
  - counter
aliases:
  - Counter Class Python
  - defaultdict
  - namedtuple
publish: true
permalink: python/the-collections-module
---

# <span class="rune">ᛟ</span> The collections Module

> *`counts[word] = counts.get(word, 0) + 1` works fine — but `collections.Counter(words)` does the entire thing, correctly, in one line, because someone already solved this exact problem so you don't have to solve it again.*

---

## 🎯 Purpose

The `collections` module provides specialized container data types that solve common, recurring problems more cleanly than the built-in `list`/`dict`/`tuple` alone — `Counter` for counting occurrences, `defaultdict` for dictionaries with automatic default values, and `namedtuple` for lightweight, self-documenting records. Each one directly replaces a manual pattern that would otherwise require several extra lines of boilerplate.

---

## 🧠 Key Ideas

- `Counter` is a `dict` subclass specialized for counting hashable objects — `Counter(iterable)` counts occurrences of each item automatically, and provides `.most_common(n)` for ranking.
- `defaultdict(factory)` is a `dict` subclass that automatically creates a default value (using `factory`, like `list` or `int`) for any missing key the moment it's first accessed — eliminating the manual `.get(key, default)` pattern from [[Dictionaries]] for the specific case of building up grouped/nested data.
- `namedtuple` creates a lightweight, immutable class with named fields, accessible both by name (`point.x`) and by position (`point[0]`) — a self-documenting alternative to a plain tuple when field meaning matters.
- `OrderedDict` predates Python 3.7's now-standard dict insertion-order guarantee — it's largely redundant for plain ordering today, but still offers a few extra methods (like `.move_to_end()`) that plain `dict` doesn't.
- `deque` (double-ended queue) provides `O(1)` append/pop from **both** ends, unlike a plain `list`, whose left-end operations are `O(n)` — the direct equivalent of preferring a linked structure for queue-like access patterns.

---

## ⚙️ How It Works

`Counter` internally is just a `dict` where keys are the distinct items and values are their counts, but it overrides construction and arithmetic to make counting trivial — `Counter(["a", "b", "a"])` builds `{"a": 2, "b": 1}` in one call, rather than requiring a manual loop with `.get(key, 0) + 1` on every iteration. `defaultdict` works by overriding what happens on a *missing key access specifically* — instead of raising `KeyError` the way a plain `dict` would, it calls the factory function to produce a default value, stores it under that key, and returns it, all automatically and transparently.

```text
# Manual counting — the pattern Counter replaces
counts = {}
for word in words:
    counts[word] = counts.get(word, 0) + 1

# Counter — the same result, one line
from collections import Counter
counts = Counter(words)
```

---

## 💻 Examples

```python
from collections import Counter, defaultdict, namedtuple, deque

# Counter — counting occurrences
words = ["apple", "banana", "apple", "cherry", "banana", "apple"]
counts = Counter(words)
print(counts)                    # Counter({'apple': 3, 'banana': 2, 'cherry': 1})
print(counts.most_common(2))      # [('apple', 3), ('banana', 2)] — top 2, ranked
print(counts["apple"])              # 3
print(counts["missing_word"])         # 0 — NO KeyError, unlike a plain dict

# defaultdict — automatic default values, great for GROUPING data
groups = defaultdict(list)
pairs = [("fruit", "apple"), ("veg", "carrot"), ("fruit", "banana")]
for category, item in pairs:
    groups[category].append(item)     # no need to check "if category not in groups" first!
print(groups)   # {'fruit': ['apple', 'banana'], 'veg': ['carrot']}

count_dict = defaultdict(int)          # int() returns 0 — a common counting pattern alternative
for word in words:
    count_dict[word] += 1               # works immediately, no .get(word, 0) needed

# namedtuple — self-documenting, lightweight records
Point = namedtuple("Point", ["x", "y"])
p = Point(3, 4)
print(p.x, p.y)     # 3 4 — accessed by NAME
print(p[0], p[1])     # 3 4 — ALSO accessible by position, like a plain tuple
print(p)                # Point(x=3, y=4) — a genuinely readable repr, for free

# deque — efficient operations at BOTH ends
queue = deque([1, 2, 3])
queue.appendleft(0)      # O(1) — a plain list's .insert(0, ...) would be O(n)
queue.append(4)
print(queue)               # deque([0, 1, 2, 3, 4])
queue.popleft()              # O(1)
print(queue)                   # deque([1, 2, 3, 4])
```

---

## 🚀 Real World Applications

- Counting word frequencies, vote tallies, or any occurrence-counting task with `Counter`, including its built-in `.most_common()` ranking
- Grouping data by a category key without manually checking for the key's existence first, using `defaultdict(list)`
- Representing simple, immutable records (coordinates, database rows, configuration entries) with `namedtuple` for better readability than an unstructured plain tuple
- Implementing an efficient queue or sliding-window buffer with `deque`, where both-ends performance genuinely matters

---

## ⚖️ Advantages

- Each specialized container solves a specific, extremely common problem more concisely and more correctly than the manual equivalent built from plain `dict`/`list`.
- `Counter` and `defaultdict` both eliminate an entire category of `KeyError`-avoidance boilerplate.
- `namedtuple` provides meaningful field names with essentially zero memory or performance overhead compared to a plain tuple.

---

## ⚠️ Limitations

- `namedtuple` instances are still immutable, exactly like regular tuples — updating a field requires creating an entirely new instance (via `._replace()`), not modifying one in place.
- `defaultdict`'s automatic default-creation-on-access can be surprising if a lookup was only meant to *check* for a key's existence — merely accessing a missing key (even just to check it) will silently create and store a default value for it.
- For genuinely complex structured data, a proper class (or a `dataclass`, Python's more modern alternative) is often clearer than a `namedtuple`, especially once behavior beyond simple data storage is needed.

---

## 🚨 Common Mistakes

- Using a plain `dict` and manually reimplementing `Counter`'s or `defaultdict`'s logic from scratch, missing the more concise and well-tested standard library tool that already solves the exact same problem.
- Checking `if key in my_defaultdict:` to test existence, not realizing the check itself doesn't trigger default creation — but a subsequent `my_defaultdict[key]` access (even just to read it) absolutely will, silently adding an unintended entry.
- Trying to modify a `namedtuple`'s field directly (`point.x = 5`), which raises `AttributeError` since namedtuples are immutable — `._replace(x=5)` (which returns a new instance) is the correct approach.
- Reaching for `deque` when a plain `list` would have sufficed (most-access patterns don't need both-ends efficiency), adding unnecessary unfamiliarity for readers without a genuine performance benefit.

---

## 📖 Further Reading

- Python documentation: `collections` — Container datatypes (the full module reference)
- Python documentation: `dataclasses` (a modern alternative to `namedtuple` for more complex structured data)

---

## 💡 Wisdom from Mímir

Before hand-rolling a counting loop or a "check if key exists, then append" grouping pattern, pause and check whether `Counter` or `defaultdict` already solves it in one line — these two specifically replace such extremely common manual patterns that reaching for them should become close to reflexive.

---

## 🔗 Related Notes

- [[Dictionaries]]
- [[Lists, Tuples, and Sets]]
- [[The datetime Module]]
- [[Python Codex]]
