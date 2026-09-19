---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Python
difficulty: Beginner
tags:
  - python-fundamentals
  - dictionaries
  - hash-tables
aliases:
  - Python dict
  - dict.get() vs KeyError
publish: true
permalink: python/dictionaries
---

# <span class="rune">ᛟ</span> Dictionaries

> *A dictionary lookup by a missing key doesn't return a friendly "not found" — it raises an exception and stops your program cold, unless you specifically told it not to.*

---

## 🎯 Purpose

`dict` is Python's built-in key-value mapping type — the direct equivalent of Java's `HashMap` — and it's one of the most heavily used data structures in the entire language, underpinning everything from simple lookups to how objects themselves store their attributes internally. Since Python 3.7, dictionaries also guarantee **insertion order** is preserved during iteration, a behavior formalized into the language spec rather than left as an implementation detail.

---

## 🧠 Key Ideas

- `{}` or `dict()` creates a dictionary; `my_dict[key] = value` sets a value, `my_dict[key]` retrieves it — but retrieving a **missing** key raises `KeyError` rather than returning `None` or a default.
- `.get(key, default)` retrieves a value safely, returning `default` (or `None` if omitted) instead of raising an exception when the key doesn't exist — the idiomatic way to avoid `KeyError` for optional lookups.
- Since Python 3.7, dictionaries **preserve insertion order** during iteration — this was an implementation detail in 3.6 and became an official language guarantee starting in 3.7.
- Dictionary keys must be **hashable** (immutable types like strings, numbers, and tuples of hashable items) — this is exactly why lists can't be used as dict keys, as covered in [[Lists, Tuples, and Sets]].
- `.items()`, `.keys()`, and `.values()` provide view objects for iterating key-value pairs, keys alone, or values alone — these views stay live, reflecting any changes made to the dictionary after the view was created.

---

## ⚙️ How It Works

A dictionary computes a hash of each key to determine where its corresponding value is stored internally, giving average `O(1)` lookup, insertion, and deletion regardless of the dictionary's size — the same underlying mechanism as Java's `HashMap`. Unlike a plain lookup with `[]` (which raises `KeyError` the instant a key isn't found, deliberately failing loud), `.get()` explicitly checks for the key's presence and returns a fallback value instead, making it the correct tool whenever a missing key is an expected, non-exceptional possibility rather than a bug.

```text
my_dict["missing_key"]           # raises KeyError — stops execution unless caught
my_dict.get("missing_key")        # returns None — no exception
my_dict.get("missing_key", 0)      # returns 0 — a specific fallback value
```

---

## 💻 Examples

```python
person = {"name": "Alice", "age": 30}

person["name"]              # "Alice"
# person["email"]           # KeyError: 'email'
person.get("email")          # None — safe
person.get("email", "N/A")    # "N/A" — safe, with a specific fallback

person["email"] = "alice@example.com"   # adds a new key
person["age"] = 31                        # updates an existing key

del person["age"]                          # removes a key
person.pop("email", None)                   # removes safely, no error if missing

# Iteration
for key in person:                # iterates KEYS by default
    print(key)

for key, value in person.items():  # both key and value together — the common idiom
    print(key, value)

for value in person.values():
    print(value)

# Checking existence
if "name" in person:               # the idiomatic way — NOT person.get("name") != None
    print("Has a name")

# Merging dictionaries (Python 3.9+)
defaults = {"theme": "light", "language": "en"}
overrides = {"theme": "dark"}
merged = defaults | overrides       # {"theme": "dark", "language": "en"}

# Building a count/lookup dict from data — a very common real pattern
words = ["apple", "banana", "apple", "cherry", "banana", "apple"]
counts = {}
for word in words:
    counts[word] = counts.get(word, 0) + 1   # .get() with a default avoids a KeyError on first occurrence
print(counts)  # {'apple': 3, 'banana': 2, 'cherry': 1}
```

---

## 🚀 Real World Applications

- Representing structured, named data (a JSON API response, a configuration object) with meaningful string keys
- Building frequency counters and lookup tables efficiently, using `.get(key, default)` to avoid manual existence checks
- Fast membership testing and value retrieval by key, relying on average `O(1)` performance regardless of dictionary size
- Using dictionaries as a simple, flexible substitute for small objects/records when a full class isn't warranted

---

## ⚖️ Advantages

- Extremely fast average-case lookup, insertion, and deletion regardless of the dictionary's size.
- Guaranteed insertion-order iteration (Python 3.7+) removes an entire class of "why is this ordering unpredictable" confusion that exists in some other languages' hash maps.
- `.get()`, `.items()`, `.setdefault()`, and other built-in methods cover the overwhelming majority of common dictionary manipulation needs without extra boilerplate.

---

## ⚠️ Limitations

- Direct `[]` access raising `KeyError` on a missing key (rather than a safe default) can catch newcomers off guard, especially coming from languages where a map lookup silently returns `null`/`None`.
- Dictionary keys must be hashable, which rules out lists (and any other mutable type) as keys without first converting them to something immutable like a tuple.
- Very large dictionaries still carry per-entry memory overhead from the underlying hash table structure, a real consideration for genuinely memory-constrained applications.

---

## 🚨 Common Mistakes

- Using `my_dict[key]` when the key's presence isn't guaranteed, causing an unhandled `KeyError` to crash the program — `.get()` (or a `try`/`except KeyError`) is the correct tool when a missing key is a legitimate possibility.
- Checking `if key in my_dict.keys():` instead of the simpler, equally correct, and more idiomatic `if key in my_dict:`.
- Forgetting `.items()` is needed to get both key and value together in a loop — iterating a dict directly (`for x in my_dict:`) only yields the keys.
- Attempting to use a list as a dictionary key and hitting a `TypeError: unhashable type: 'list'`, without realizing a tuple would work in its place.

---

## 📖 Further Reading

- Python documentation: "The Python Tutorial" — Section 5.5, "Dictionaries"
- Python documentation: `dict` — Mapping Types

---

## 💡 Wisdom from Mímir

Default to `.get(key, default)` the instant a key's presence isn't guaranteed — it turns a potential unhandled crash into a deliberate, controlled fallback with a single method call, and it reads more clearly than a `try`/`except KeyError` for the common case of a simple missing-key default.

---

## 🔗 Related Notes

- [[Lists, Tuples, and Sets]]
- [[Dictionary and Set Comprehensions]]
- [[The collections Module]]
- [[Python Codex]]
