---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Python
difficulty: Beginner
tags:
  - python-fundamentals
  - control-flow
  - indentation
aliases:
  - Python Indentation Rules
  - for-else and while-else
publish: true
permalink: python/control-flow-if-for-and-while
---

# <span class="rune">ᛟ</span> Control Flow - if, for, and while

> *Python doesn't use braces to mark a block — the indentation itself IS the block. Get it wrong, and the interpreter doesn't guess your intent; it just tells you the code is broken.*

---

## 🎯 Purpose

Python's control flow constructs — `if`/`elif`/`else`, `for`, and `while` — are conceptually familiar from other languages, but Python enforces two things almost no other mainstream language does: **indentation defines block structure** (not braces), and both loop types support an unusual `else` clause that runs only if the loop completes *without* hitting a `break`.

---

## 🧠 Key Ideas

- Indentation is **syntactically significant** — it's not a style preference, it's how Python determines which statements belong to which block. Mixing tabs and spaces, or inconsistent indentation, is a syntax error.
- Python has no `switch` statement in the classic sense; a chain of `if`/`elif`/`else` is idiomatic, and Python 3.10+ added `match`/`case` for structural pattern matching as a more powerful alternative.
- `for` loops in Python iterate directly over items in a sequence (`for item in iterable`) — there's no traditional C-style `for (i=0; i<n; i++)` form; `range(n)` provides index-based iteration when actually needed.
- Both `for` and `while` support an **`else` clause** — it runs only if the loop completes normally (without an intervening `break`), which surprises nearly everyone the first time they encounter it.
- Truthiness governs `if`/`while` conditions the same way it does in most dynamic languages: `0`, `0.0`, `""`, `[]`, `{}`, `None`, and `False` are all falsy; virtually everything else is truthy.

---

## ⚙️ How It Works

Rather than delimiting blocks with `{}` or `begin`/`end` keywords, Python uses a colon followed by a consistently indented block — the interpreter tracks indentation level as it parses, and a change in indentation level signals the end of the current block. The `for`/`while`...`else` construct makes more sense once reframed: the `else` doesn't mean "if the loop didn't run" — it means "if the loop finished on its own, without being cut short by `break`" — making it genuinely useful for search-style loops that need to distinguish "found it and broke early" from "searched everything and found nothing."

```python
for item in items:
    if item == target:
        print("Found it!")
        break
else:
    print("Never found it — this runs ONLY because break was never hit")
```

---

## 💻 Examples

```python
# if/elif/else
score = 85
if score >= 90:
    print("A")
elif score >= 80:
    print("B")
else:
    print("C or below")

# Python 3.10+ structural pattern matching
match score:
    case s if s >= 90:
        print("A")
    case s if s >= 80:
        print("B")
    case _:
        print("C or below")

# for loop — iterates over ITEMS directly
for fruit in ["apple", "banana", "cherry"]:
    print(fruit)

for i in range(5):          # index-based iteration when actually needed
    print(i)                 # 0, 1, 2, 3, 4

for i, fruit in enumerate(["apple", "banana"]):  # index AND value together
    print(i, fruit)

# while loop
count = 0
while count < 3:
    print(count)
    count += 1               # Python has NO ++ or -- operators

# for-else — the unusual but genuinely useful pattern
def find_first_even(numbers):
    for n in numbers:
        if n % 2 == 0:
            print(f"Found: {n}")
            break
    else:
        print("No even number found")

find_first_even([1, 3, 5, 7])   # "No even number found" — else DOES run, no break occurred
find_first_even([1, 3, 4, 5])   # "Found: 4" — else does NOT run, break occurred
```

---

## 🚀 Real World Applications

- Using `for`/`else` for search loops that need to cleanly distinguish "found and stopped early" from "searched the whole thing and found nothing"
- Choosing `match`/`case` for cleanly expressing multi-branch logic based on a value's shape or content, especially with structural destructuring
- Iterating directly over collection items (the idiomatic Python style) rather than manually indexing, except when the index itself is genuinely needed (`enumerate()`)
- Relying on truthiness checks (`if my_list:` instead of `if len(my_list) > 0:`) as the idiomatic, "Pythonic" style

---

## ⚖️ Advantages

- Enforced indentation eliminates an entire category of bugs from mismatched or ambiguous braces, and makes every Python codebase visually consistent by necessity.
- Direct iteration over items (rather than manual indexing) reads cleanly and avoids off-by-one errors entirely for the common case.
- `for`/`while`...`else` provides a clean, native way to express "did this loop find something or not" without a separate flag variable.

---

## ⚠️ Limitations

- Indentation-based syntax means copy-pasting code between editors with different tab/space settings can silently break — a real, recurring practical annoyance.
- The `for`/`while`...`else` construct is genuinely unintuitive on first encounter and is one of the more commonly misunderstood features in the language, even among experienced developers coming from other languages.
- Lack of a classic C-style indexed `for` loop can feel unfamiliar to developers coming from languages where that's the default form, though `range()` fills the same need.

---

## 🚨 Common Mistakes

- Mixing tabs and spaces in the same file, causing a confusing `IndentationError` (or, in older Python versions, silently different block structure than intended).
- Assuming `else` after a loop means "if the loop didn't execute at all" — it actually means "if the loop completed without hitting `break`," which is a meaningfully different condition.
- Manually indexing into a list with a counter variable (`while i < len(items): item = items[i]; i += 1`) when a plain `for item in items:` would be simpler, safer, and more idiomatic.
- Forgetting there's no `++`/`--` operator in Python — `i++` is a syntax error; `i += 1` is required.

---

## 📖 Further Reading

- Python documentation: "The Python Tutorial" — Section 4, "More Control Flow Tools"
- PEP 634: "Structural Pattern Matching"

---

## 💡 Wisdom from Mímir

The first time `for`/`else` produces an unexpected result, resist the instinct to call it broken — reread it as "else = no break happened," not "else = the loop was empty." Once that reframe clicks, it becomes one of the more genuinely elegant search-loop patterns in the language rather than a confusing surprise.

---

## 🔗 Related Notes

- [[Variables, Types, and Dynamic Typing]]
- [[List Comprehensions and Generator Expressions]]
- [[Python Codex]]
