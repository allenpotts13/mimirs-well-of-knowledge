---
publish: true
status: published
permalink: python/python-cheat-sheet
---

# <span class="rune">ᛊ</span> Python Cheat Sheet

> Dense reference across core Python. Each section links back to the full [[Python Codex|concept note]] for depth.

---

## Types & Dynamic Typing

```python
x = 5
x = "hello"          # rebinding is FINE — the NAME was never typed, only the object is

type(x)                # actual runtime type
isinstance(x, str)       # the idiomatic check — respects subclasses, unlike type(x) == str

def greet(name: str) -> str:   # type hints — documentation only, NOT enforced at runtime
    return name
```

See: [[Variables, Types, and Dynamic Typing]]

---

## Control Flow

```python
if score >= 90: grade = "A"
elif score >= 80: grade = "B"
else: grade = "C"

for item in items: ...                # iterate items DIRECTLY
for i, item in enumerate(items): ...   # index + value together
for i in range(5): ...                  # index-based when actually needed

while condition: ...
for item in items:
    if item == target: break
else:
    print("never found it")             # runs ONLY if break was NEVER hit
```

See: [[Control Flow - if, for, and while]]

---

## Functions

```python
def greet(name, greeting="Hello"):     # default args
    return f"{greeting}, {name}!"

greet(name="Bo", greeting="Hi")          # keyword args — any order

# THE TRAP — mutable default argument
def add(item, basket=[]):                # basket created ONCE, shared across EVERY call
    basket.append(item); return basket
def add_fixed(item, basket=None):          # THE FIX
    if basket is None: basket = []
    basket.append(item); return basket

def total(*args): return sum(args)              # collects positional args into a TUPLE
def info(**kwargs): return kwargs                # collects keyword args into a DICT
add(*[1,2,3])                                       # UNPACKS a list into positional args
add(**{"a":1,"b":2})                                  # UNPACKS a dict into keyword args
```

See: [[Functions and Default Arguments]] · [[Args and Kwargs]]

---

## Collections

```python
my_list = [1, 2, 3]              # ordered, MUTABLE, duplicates OK
my_tuple = (1, 2, 3)               # ordered, IMMUTABLE, hashable — usable as a dict key
my_set = {1, 2, 2, 3}                # unordered, unique — {1, 2, 3}, fast O(1) membership

my_dict = {"a": 1}
my_dict["a"]              # raises KeyError if missing
my_dict.get("a", 0)         # SAFE — returns 0 if missing
for k, v in my_dict.items(): ...

d1 | d2                     # merge dicts (3.9+)
```

**Sequence slicing** (works on list/tuple/str identically):

```python
s = [10, 20, 30, 40, 50]
s[1:3]     # [20, 30]  — stop is EXCLUSIVE
s[-2:]      # [40, 50]  — negative index from the end
s[::-1]      # reversed
s[10:20]      # []  — out of range NEVER errors (unlike s[10] direct indexing)
```

See: [[Lists, Tuples, and Sets]] · [[Dictionaries]] · [[Slicing]]

---

## f-strings

```python
name, age = "Alice", 30
f"{name} is {age}"           # "Alice is 30"
f"{age + 1}"                   # any EXPRESSION works inside {}
f"{price:.2f}"                   # 2 decimal places
f"{1000000:,}"                     # 1,000,000 — thousands separator
f"{x=}"                              # debug shortcut — shows "x=<value>"
```

See: [[String Formatting and f-strings]]

---

## Comprehensions

```python
[x**2 for x in range(10)]                    # list comp
[x for x in range(20) if x % 2 == 0]           # with a filter
(x**2 for x in range(10))                       # generator — LAZY, one value at a time, ONE-TIME use

{k: v for k, v in pairs}          # dict comprehension — colon = dict
{x for x in items}                  # set comprehension — no colon = set, auto-dedupes
{}                                     # ALWAYS an empty dict, NEVER a set — use set() for that
```

See: [[List Comprehensions and Generator Expressions]] · [[Dictionary and Set Comprehensions]]

---

## Classes & OOP

```python
class Dog(Animal):                          # inheritance
    def __init__(self, name, breed):
        super().__init__(name)                # NOT automatic — must call explicitly, unlike Java
        self.breed = breed

    def speak(self):                             # override — NO special keyword needed
        super().speak()
        print(f"{self.name} says Woof!")

isinstance(rex, Animal)     # True — Dog IS-A Animal
```

```python
class Point:
    def __init__(self, x, y): self.x, self.y = x, y
    def __str__(self): return f"({self.x}, {self.y})"        # print(obj), str(obj)
    def __repr__(self): return f"Point({self.x}, {self.y})"    # debugging / interpreter — define AT LEAST this
    def __eq__(self, other): return (self.x, self.y) == (other.x, other.y)
    def __hash__(self): return hash((self.x, self.y))            # REQUIRED if you define __eq__ and need hashing
```

```python
class Circle:
    @classmethod
    def from_diameter(cls, d): return cls(d / 2)     # cls() respects subclasses — never hardcode the class name

    @staticmethod
    def is_valid(r): return r > 0                       # no self/cls — a plain function, namespaced here

    @property
    def area(self): return 3.14159 * self._r ** 2         # accessed like an attribute: circle.area
```

**Privacy is convention only** — `_name` = "internal, please don't touch" (not enforced). `__name` = name-mangled to `_ClassName__name` (harder to reach, not impossible — NOT true privacy).

```python
from abc import ABC, abstractmethod
class Shape(ABC):
    @abstractmethod
    def area(self): pass       # subclasses MUST implement — TypeError at instantiation otherwise
```

See: [[Classes and Objects in Python]] · [[Inheritance and super()]] · [[Dunder Methods - __init__, __str__, __repr__, and __eq__]] · [[Class Methods, Static Methods, and Properties]] · [[Encapsulation Conventions - Single and Double Underscore]] · [[Abstract Base Classes]] · [[Multiple Inheritance and the MRO]]

---

## Lambdas, Decorators, Context Managers

```python
sorted(people, key=lambda p: p.age)          # idiomatic — inline, throwaway, single EXPRESSION only
list(map(lambda x: x * 2, nums))
list(filter(lambda x: x % 2 == 0, nums))
```

```python
import functools
def logger(func):
    @functools.wraps(func)              # ALWAYS include — preserves __name__/docstring
    def wrapper(*args, **kwargs):
        result = func(*args, **kwargs)
        return result
    return wrapper

@logger
def greet(name): print(f"Hi {name}")
```

```python
with open("file.txt") as f:          # guaranteed cleanup, even on exception — like try-with-resources
    data = f.read()

from contextlib import contextmanager
@contextmanager
def my_ctx():
    print("setup")
    yield              # code BEFORE yield = setup, AFTER = cleanup
    print("cleanup")
```

See: [[Lambda Functions in Python]] · [[Decorators]] · [[Context Managers and the with Statement]]

---

## Iterators & Generators

```python
def count_up_to(n):
    i = 1
    while i <= n:
        yield i           # PAUSES here, resumes on next next() call
        i += 1

for num in count_up_to(5): ...    # for loops drive generators automatically
gen = count_up_to(3)
next(gen)                            # manual driving — StopIteration when exhausted
```

**A generator can only be consumed ONCE.**

See: [[Iterators and Generators]]

---

## Unpacking & Walrus

```python
a, b = 1, 2
a, b = b, a                      # swap, no temp variable

first, *middle, last = [1,2,3,4,5]   # starred unpacking — collects "the rest"

if (data := fetch()):              # walrus — assign AND use in the same expression
    process(data)                    # avoids computing/calling fetch() twice
```

See: [[Unpacking and the Walrus Operator]]

---

## Error Handling

```python
try:
    risky()
except ValueError:
    ...
except (TypeError, KeyError) as e:     # multiple types in one block
    ...
else:
    only_if_no_exception()               # NO Java equivalent — separates success-only logic
finally:
    always_runs()

# NEVER: except:  (bare except — catches EVERYTHING including KeyboardInterrupt)
```

```python
class InsufficientFundsError(Exception):
    def __init__(self, msg, shortfall):
        super().__init__(msg)
        self.shortfall = shortfall

raise DataError("failed") from original_exception   # explicit chaining — shows in traceback
```

**Python has NO checked exceptions** — nothing is enforced by the compiler, ever.

```python
assert condition, "message"     # NEVER use for input validation/security — stripped ENTIRELY by python -O
```

**EAFP** (try it, catch failure) is preferred over **LBYL** (check first) in idiomatic Python.

See: [[Exception Handling - try, except, else, and finally]] · [[Custom Exceptions in Python]] · [[assert and Defensive Programming]]

---

## Modules & Environment

```python
import module_name
from module_name import specific_name
import module_name as alias

if __name__ == "__main__":     # entry point — runs ONLY when executed directly, not on import
    main()
```

```bash
python -m venv venv                    # create an isolated environment PER project
source venv/bin/activate                 # Windows: venv\Scripts\activate
pip install package_name
pip freeze > requirements.txt              # capture exact dependencies
pip install -r requirements.txt              # recreate them elsewhere
```

See: [[Modules and the import System]] · [[Virtual Environments and pip]] · [[The if __name__ == __main__ Idiom]]

---

## Files, collections module, datetime, JSON

```python
with open("f.txt", "r") as f: content = f.read()
with open("f.txt", "w") as f: f.write("x")     # 'w' TRUNCATES IMMEDIATELY — use 'a' to append safely

from pathlib import Path
p = Path("data") / "file.txt"       # cross-platform joining
p.read_text(); p.exists()
```

```python
from collections import Counter, defaultdict, namedtuple, deque
Counter(words).most_common(3)                    # top 3 occurrences
groups = defaultdict(list)                         # no KeyError on first access to a new key
Point = namedtuple("Point", ["x", "y"])              # lightweight, named, immutable record
deque().appendleft(x)                                  # O(1) at BOTH ends, unlike a list
```

```python
from datetime import datetime, timedelta, timezone
now = datetime.now()                        # NAIVE — no timezone attached
aware = datetime.now(timezone.utc)            # AWARE — explicit, safe to compare across timezones
now.strftime("%Y-%m-%d")                        # format → string
datetime.strptime("2026-09-19", "%Y-%m-%d")       # parse string → datetime
```

```python
import json
json.dumps(obj)          # object → STRING   ("s" = string)
json.dump(obj, file)       # object → FILE
json.loads(string)           # STRING → object
json.load(file)                # FILE → object
```

See: [[Working with Files in Python]] · [[The collections Module]] · [[The datetime Module]] · [[Working with JSON in Python]]

---

## 🔗 Related Notes

- [[Python Codex]]
- [[Java Cheat Sheet]]
