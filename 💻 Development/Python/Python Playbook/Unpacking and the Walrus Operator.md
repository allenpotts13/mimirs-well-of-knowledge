---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Python
difficulty: Intermediate
tags:
  - python-idioms
  - unpacking
  - walrus-operator
aliases:
  - Walrus Operator
  - ":= Operator"
  - Extended Unpacking
publish: true
permalink: python/unpacking-and-the-walrus-operator
---

# <span class="rune">ᛟ</span> Unpacking and the Walrus Operator

> *The walrus operator doesn't just assign a value — it assigns AND evaluates to that value, in the same breath, which is exactly what lets it slot into places a plain assignment statement never could.*

---

## 🎯 Purpose

Python's unpacking syntax lets you assign multiple values from a sequence in a single statement, with a flexible "collect the rest" star syntax for variable-length splits. The **walrus operator** (`:=`, Python 3.8+) is a distinct but related idiom — it lets you assign a value to a name *and* use that value as part of a larger expression, in one place, most commonly inside a `while` loop condition or a comprehension.

---

## 🧠 Key Ideas

- Basic unpacking: `a, b = (1, 2)` assigns each element to a name, matching sequence length exactly — a mismatch raises `ValueError`.
- Extended (starred) unpacking: `first, *middle, last = [1, 2, 3, 4, 5]` collects "everything else" into a list bound to the starred name, while the non-starred names still grab specific positions.
- The **walrus operator**, `name := expression`, both assigns `expression`'s value to `name` and evaluates to that same value — usable inside an expression context (an `if` condition, a `while` condition, a comprehension) where a plain `=` assignment statement isn't syntactically allowed at all.
- Unpacking works with any iterable, not just tuples — lists, strings, and the results of function calls returning multiple values (which are really just tuples) all support it identically.
- The walrus operator is most valuable specifically for avoiding **computing or calling something twice** — once to check a condition, and again to use the result — a pattern that previously required either redundant code or restructuring the logic.

---

## ⚙️ How It Works

Standard unpacking (`a, b = my_tuple`) works by matching the left side's names one-to-one against the right side's iterable — Python raises an error immediately if the counts don't match. Starred unpacking relaxes this: exactly one name can be prefixed with `*`, and it absorbs however many elements are left over after the other named positions are satisfied. The walrus operator solves a different problem entirely: ordinary Python statements (like `x = 5`) are *statements*, not *expressions* — they can't appear inside a larger expression like an `if` condition — but `(x := 5)` is genuinely an expression that both performs the assignment and evaluates to the assigned value, letting it be used exactly where a plain assignment couldn't.

```text
# Without the walrus — the value gets computed/read TWICE
data = fetch_data()
if data:
    process(data)

# With the walrus — computed ONCE, used immediately in the condition
if (data := fetch_data()):
    process(data)
```

---

## 💻 Examples

```python
# Basic unpacking
a, b = (1, 2)
x, y, z = [10, 20, 30]

# Swapping values — a classic idiomatic use of unpacking
a, b = 1, 2
a, b = b, a          # swapped, with no temporary variable needed
print(a, b)            # 2 1

# Starred (extended) unpacking
first, *middle, last = [1, 2, 3, 4, 5]
print(first)   # 1
print(middle)   # [2, 3, 4]
print(last)      # 5

first, *rest = [1, 2, 3]
print(rest)   # [2, 3]

*rest, last = [1, 2, 3]
print(rest)     # [1, 2]

# Ignoring values with underscore convention
name, _, age = ("Alice", "ignored", 30)

# Function calls "returning multiple values" — really just returning a tuple
def get_min_max(numbers):
    return min(numbers), max(numbers)

lo, hi = get_min_max([4, 2, 8, 1])

# THE WALRUS OPERATOR — assignment inside an expression

# In a while loop — avoids calling input() twice
# while (line := input("Enter text: ")) != "quit":
#     print(f"You entered: {line}")

# In a comprehension — avoids computing something twice per iteration
data = [1, 2, 3, 4, 5]
results = [y for x in data if (y := x * 2) > 4]
print(results)   # [6, 8, 10]

# In a regular if condition — compute once, use immediately
import re
text = "The year is 2026"
if (match := re.search(r"\d+", text)):
    print(f"Found: {match.group()}")   # "Found: 2026" — match computed ONCE, reused here
```

---

## 🚀 Real World Applications

- Cleanly swapping two variables' values in a single line without a temporary variable
- Splitting a sequence into "first element(s), everything else" or "everything else, last element(s)" with starred unpacking
- Avoiding a redundant second function call or expensive computation inside an `if`/`while` condition and its body, using the walrus operator
- Extracting multiple return values from a function call directly into meaningfully-named variables

---

## ⚖️ Advantages

- Unpacking eliminates verbose manual indexing when extracting multiple values from a known-shape sequence.
- Starred unpacking cleanly expresses "the first/last N, plus everything else" without manual slicing.
- The walrus operator avoids the classic "compute it once for the check, compute it again to use it" redundancy, in both readability and performance terms.

---

## ⚠️ Limitations

- Basic unpacking requires an exact count match (unless starred) — a sequence with more or fewer elements than expected raises `ValueError` immediately.
- The walrus operator, introduced relatively recently (Python 3.8), isn't available in older codebases or environments still targeting earlier Python versions.
- Overusing the walrus operator in already-complex expressions can hurt readability rather than help it — it's most valuable specifically for the "avoid computing twice" case, not as a general style choice.

---

## 🚨 Common Mistakes

- Mismatching the number of names on the left with the number of elements on the right during unpacking, causing a `ValueError: too many values to unpack` (or "not enough values").
- Using more than one starred name in a single unpacking statement (`*a, *b = [1,2,3]`), which is a `SyntaxError` — only one starred target is allowed per unpacking.
- Forgetting the walrus operator requires parentheses in many contexts (`if (x := compute()):`) for the assignment to parse correctly as part of the larger expression.
- Reaching for the walrus operator purely for its novelty, in situations where it doesn't actually eliminate any redundant computation and just adds unfamiliar syntax for no real benefit.

---

## 📖 Further Reading

- Python documentation: "The Python Tutorial" — Section 5.3, "Tuples and Sequences" (unpacking)
- PEP 572: "Assignment Expressions" (the walrus operator proposal)

---

## 💡 Wisdom from Mímir

Reach for the walrus operator specifically when you'd otherwise have to compute or call something twice — once in a condition, again to use the result. That's its actual purpose, and outside of that specific pattern, a plain assignment statement is almost always the clearer, more familiar choice.

---

## 🔗 Related Notes

- [[Lists, Tuples, and Sets]]
- [[Args and Kwargs]]
- [[List Comprehensions and Generator Expressions]]
- [[Python Codex]]
