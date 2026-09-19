---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Python
difficulty: Beginner
tags:
  - standard-library
  - json-module
  - serialization
aliases:
  - json.dumps vs json.dump
  - Python JSON Module
publish: true
permalink: python/working-with-json-in-python
---

# <span class="rune">ᛟ</span> Working with JSON in Python

> *`dumps` and `dump` differ by exactly one letter and do genuinely different things — one produces a string in memory, the other writes straight to a file. Confuse them, and the error message won't obviously point back to that one missing letter.*

---

## 🎯 Purpose

The `json` module converts between Python objects and JSON text — `dict`/`list`-based Python data structures map naturally onto JSON's object/array structure, making Python one of the most JSON-friendly languages to work in. The module provides four core functions, distinguished by direction (encode vs. decode) and target (string vs. file), and mixing up which is which is a common early source of confusion.

---

## 🧠 Key Ideas

- `json.dumps(obj)` — converts a Python object into a JSON **string** ("dump to string").
- `json.dump(obj, file)` — converts a Python object and writes it **directly to a file** ("dump to a file object") — note the missing `s`.
- `json.loads(string)` — parses a JSON **string** into a Python object ("load from string").
- `json.load(file)` — parses JSON directly **from a file** ("load from a file object") — again, note the missing `s`.
- Python's type mapping to JSON is mostly intuitive (`dict`→object, `list`→array, `str`→string, `int`/`float`→number, `True`/`False`→`true`/`false`, `None`→`null`) — but some Python types have **no** direct JSON equivalent at all (tuples become JSON arrays, indistinguishable from lists once serialized; sets aren't JSON-serializable by default; custom objects need a special encoder).

---

## ⚙️ How It Works

The naming convention across all four functions is consistent once noticed: functions ending in `s` (`dumps`, `loads`) operate on **strings** in memory; functions without the `s` (`dump`, `load`) operate directly on an already-open **file object**, reading or writing the JSON text as a side effect rather than returning/accepting it as a string value. `dumps`/`dump` walk through the Python object recursively, converting each value according to the standard type mapping — and raise `TypeError` immediately if they encounter a type with no defined JSON representation (like a raw `set`) unless a custom encoder is supplied to handle it.

```text
dumps(obj)   → returns a STRING           loads(string) → returns a Python OBJECT
dump(obj, f)  → writes to a FILE            load(f)        → reads from a FILE, returns an OBJECT
     ^ no "s" = works with a FILE, not a string
```

---

## 💻 Examples

```python
import json

data = {
    "name": "Alice",
    "age": 30,
    "is_admin": False,
    "tags": ["python", "developer"],
    "address": None
}

# dumps() — Python object → JSON STRING
json_string = json.dumps(data)
print(json_string)   # '{"name": "Alice", "age": 30, "is_admin": false, "tags": ["python", "developer"], "address": null}'

# Pretty-printed, indented output
pretty = json.dumps(data, indent=2)
print(pretty)

# loads() — JSON STRING → Python object
parsed = json.loads(json_string)
print(parsed["name"])   # "Alice"
print(type(parsed))       # <class 'dict'>

# dump() — Python object → written DIRECTLY to a FILE
with open("data.json", "w") as f:
    json.dump(data, f, indent=2)

# load() — read DIRECTLY from a FILE → Python object
with open("data.json", "r") as f:
    loaded_data = json.load(f)
print(loaded_data)

# What CAN'T be serialized directly
# json.dumps({1, 2, 3})              # TypeError — sets have no JSON equivalent
# json.dumps(datetime.now())          # TypeError — datetime isn't natively JSON-serializable either

# Handling non-serializable types with a custom default
from datetime import datetime

def custom_encoder(obj):
    if isinstance(obj, datetime):
        return obj.isoformat()          # convert to a JSON-friendly string representation
    raise TypeError(f"Type {type(obj)} not serializable")

json.dumps({"created": datetime.now()}, default=custom_encoder)

# Tuples become indistinguishable from lists once round-tripped
original = {"point": (3, 4)}      # a TUPLE
round_tripped = json.loads(json.dumps(original))
print(type(round_tripped["point"]))   # <class 'list'> — the tuple is now a LIST, distinction lost
```

---

## 🚀 Real World Applications

- Parsing JSON API responses into native Python dicts/lists for easy manipulation
- Serializing application data (configuration, cached results, structured output) to a JSON file for persistence
- Pretty-printing JSON output for readable logs, debugging, or human-facing configuration files
- Providing a custom encoder to handle Python types (dates, custom classes) that don't have a built-in JSON representation

---

## ⚖️ Advantages

- The type mapping between Python and JSON is intuitive enough for most everyday data structures, requiring no special handling in the common case.
- Built into the standard library — no external dependency needed for basic JSON encoding/decoding.
- `indent=` provides easy, readable pretty-printing for debugging or human-facing output with zero extra effort.

---

## ⚠️ Limitations

- Several common Python types have no native JSON equivalent (`set`, `datetime`, custom class instances) and require explicit handling via a custom `default` function or `object_hook`.
- Round-tripping a Python object through JSON loses type distinctions the JSON spec itself doesn't support — tuples become lists, and there's no way to distinguish them again after deserialization without extra application-level convention.
- Very large JSON files loaded via `json.load()`/`loads()` are read entirely into memory at once — genuinely huge JSON data may need a streaming parser instead.

---

## 🚨 Common Mistakes

- Confusing `dumps`/`dump` (or `loads`/`load`) and passing a string where a file object was expected, or vice versa, resulting in a confusing `AttributeError` or `TypeError` that doesn't obviously point back to the missing/extra `s`.
- Attempting to serialize a `set`, a `datetime`, or a custom class instance directly without a custom encoder, hitting `TypeError: Object of type X is not JSON serializable`.
- Assuming a tuple round-trips through JSON as a tuple — it becomes an indistinguishable list, silently losing that type information.
- Forgetting `indent=` produces genuinely different (larger, human-readable) output than the compact default — using it unnecessarily for machine-to-machine data transfer wastes bandwidth/storage for no real benefit.

---

## 📖 Further Reading

- Python documentation: `json` — JSON encoder and decoder (the full module reference, including the type conversion table)

---

## 💡 Wisdom from Mímir

Remember the naming pattern as "the `s` means string" — `dumps`/`loads` work with strings in memory, while `dump`/`load` (no `s`) work directly with an already-open file. Getting this backwards is one of the most common small mistakes with the `json` module, and the resulting error rarely spells out the actual cause clearly.

---

## 🔗 Related Notes

- [[Working with Files in Python]]
- [[Dictionaries]]
- [[String Formatting and f-strings]]
- [[Python Codex]]
