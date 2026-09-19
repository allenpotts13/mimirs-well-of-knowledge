---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Python
difficulty: Beginner
tags:
  - python-fundamentals
  - lists
  - tuples
  - sets
aliases:
  - Mutable vs Immutable Sequences
  - Tuple vs List Python
publish: true
permalink: python/lists-tuples-and-sets
---

# <span class="rune">ᛟ</span> Lists, Tuples, and Sets

> *A tuple isn't just "an immutable list" — that framing misses the real distinction. A tuple is for a fixed-shape record; a list is for a variable-length collection of similar things. The immutability is a consequence of that difference, not the point of it.*

---

## 🎯 Purpose

Python provides three core built-in collection types with genuinely different purposes: `list` (ordered, mutable, allows duplicates), `tuple` (ordered, **immutable**, allows duplicates), and `set` (unordered, mutable, **no duplicates**). Choosing the right one isn't just a performance detail — it communicates intent about whether a collection's contents should change, whether order matters, and whether duplicates are meaningful.

---

## 🧠 Key Ideas

- `list` — created with `[]`, ordered, **mutable** (items can be added, removed, or changed after creation), allows duplicates.
- `tuple` — created with `()`, ordered, **immutable** (once created, it cannot be changed — no append, no item reassignment), allows duplicates.
- `set` — created with `{}` or `set()`, **unordered**, mutable, automatically **eliminates duplicates**, and supports fast membership testing (`in`) and mathematical set operations (union, intersection, difference).
- Because tuples are immutable, they're **hashable** (as long as their contents are also hashable) and can be used as dictionary keys or set elements — lists cannot, since they're mutable.
- A tuple is often used to represent a fixed, heterogeneous record (`("Alice", 30, "Engineer")` — name, age, job) where each position has a specific meaning, while a list represents a homogeneous, variable-length collection of similar items (`["Alice", "Bob", "Cara"]`).

---

## ⚙️ How It Works

Lists are implemented as dynamic arrays internally (similar in spirit to Java's `ArrayList`), supporting efficient indexed access and append operations, with the ability to grow, shrink, and have individual elements reassigned freely. Tuples, once constructed, have no methods that modify their contents at all — this immutability is exactly what makes them safely hashable (their contents can never change after their hash is computed, so using one as a dict key or set element is always safe). Sets are implemented using a hash table internally, exactly like a dict with only keys and no values — which is why set membership testing (`x in my_set`) is fast (`O(1)` average) regardless of the set's size, unlike a list's `O(n)` linear search.

```text
my_list = [1, 2, 3]
my_list.append(4)        # FINE — lists are mutable
my_list[0] = 99           # FINE — item reassignment allowed

my_tuple = (1, 2, 3)
my_tuple.append(4)         # AttributeError — tuples have no append method at all
my_tuple[0] = 99            # TypeError — tuples don't support item assignment

my_set = {1, 2, 2, 3}         # duplicates AUTOMATICALLY collapsed
print(my_set)                  # {1, 2, 3}
```

---

## 💻 Examples

```python
# Lists — ordered, mutable
fruits = ["apple", "banana", "cherry"]
fruits.append("date")
fruits[0] = "avocado"
fruits.remove("banana")
print(fruits)  # ['avocado', 'cherry', 'date']

# Tuples — ordered, immutable, often used for fixed records
point = (3, 4)
x, y = point            # unpacking — very common tuple use case
person = ("Alice", 30, "Engineer")

def get_min_max(numbers):
    return (min(numbers), max(numbers))   # returning multiple values as a tuple

lo, hi = get_min_max([4, 2, 8, 1])   # unpacked directly

# Tuples as dictionary keys — only possible BECAUSE they're immutable/hashable
locations = {
    (40.7128, -74.0060): "New York",
    (34.0522, -118.2437): "Los Angeles"
}

# Sets — unordered, unique, fast membership testing
unique_ids = {101, 102, 103, 102}
print(unique_ids)          # {101, 102, 103} — duplicate silently collapsed
print(102 in unique_ids)    # True — O(1) average lookup

# Set operations
a = {1, 2, 3}
b = {2, 3, 4}
print(a | b)   # {1, 2, 3, 4}  — union
print(a & b)   # {2, 3}         — intersection
print(a - b)   # {1}             — difference
print(a ^ b)   # {1, 4}           — symmetric difference (in one but not both)
```

---

## 🚀 Real World Applications

- Using tuples to return multiple values from a function cleanly, or to represent a fixed-shape coordinate/record
- Using tuples as dictionary keys when a composite (multi-part) key is needed, something a list could never do
- Using sets for fast deduplication of a collection, or for efficient membership testing against a large collection of values
- Using set operations (union, intersection, difference) to compare two collections — finding common elements, unique elements, or differences between them

---

## ⚖️ Advantages

- Tuples' immutability provides a lightweight guarantee that a fixed collection of values won't accidentally be modified elsewhere in the code.
- Sets provide dramatically faster membership testing than lists for large collections, along with automatic deduplication.
- Lists' flexibility (growing, shrinking, reordering) makes them the natural default choice for most everyday variable-length collections.

---

## ⚠️ Limitations

- Tuples' immutability means any "modification" requires creating an entirely new tuple — there's no way to append or change an element in place.
- Sets provide no guaranteed ordering at all — iterating a set's contents can produce a different order than insertion, and definitely no indexed access (`my_set[0]` doesn't work).
- Choosing a list where a tuple's immutability guarantee would better communicate intent (or vice versa) is purely a design choice the language doesn't enforce, requiring some discipline.

---

## 🚨 Common Mistakes

- Trying to use a list as a dictionary key or set element, hitting a `TypeError: unhashable type: 'list'` — a tuple is required instead, precisely because of its immutability.
- Assuming a set preserves insertion order — it doesn't; if order matters alongside uniqueness, a different approach (like `dict.fromkeys()`, which does preserve insertion order in modern Python) is needed.
- Trying to index into a set (`my_set[0]`), which fails — sets have no defined order and therefore no indexed access at all.
- Using a tuple purely out of habit for a variable-length collection that's actually going to grow or shrink — a list is the correct choice whenever the collection's size or contents genuinely need to change.

---

## 📖 Further Reading

- Python documentation: "The Python Tutorial" — Section 5, "Data Structures"
- Python documentation: `set` and `frozenset` — Set Types

---

## 💡 Wisdom from Mímir

Before reaching for a list by default, ask two questions: does the order and count of items ever need to change (list), and is uniqueness the actual point (set)? A tuple's immutability isn't a limitation to work around — it's a signal to the next reader that this specific collection of values is a fixed, complete record, not a work in progress.

---

## 🔗 Related Notes

- [[Dictionaries]]
- [[Slicing]]
- [[List Comprehensions and Generator Expressions]]
- [[Python Codex]]
