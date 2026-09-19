---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Python
difficulty: Beginner
tags:
  - python-fundamentals
  - strings
  - f-strings
aliases:
  - f-string
  - str.format() vs f-strings
publish: true
permalink: python/string-formatting-and-f-strings
---

# <span class="rune">ᛟ</span> String Formatting and f-strings

> *An f-string doesn't just insert a value into text — the expression inside the braces is real, live Python code, evaluated at that exact point, which is why it can hold a calculation just as easily as a variable name.*

---

## 🎯 Purpose

Python has accumulated several string formatting mechanisms over its history — old-style `%` formatting, `.format()`, and modern **f-strings** (formatted string literals, Python 3.6+) — and f-strings have become the clearly preferred, idiomatic choice for nearly all new code, thanks to their readability and the fact that they can embed *any* valid Python expression directly inline.

---

## 🧠 Key Ideas

- An f-string is written with an `f` prefix before the opening quote: `f"Hello, {name}!"` — anything inside `{}` is evaluated as a live Python expression, not just a plain variable name.
- Format specifiers control presentation directly inside the braces: `f"{value:.2f}"` (2 decimal places), `f"{value:,}"` (thousands separator), `f"{value:>10}"` (right-aligned, width 10).
- The older `.format()` method (`"{}".format(value)`) and even older `%`-style formatting (`"%s" % value`) still appear frequently in existing/legacy code and library documentation, so recognizing them remains useful even though f-strings are preferred for new code.
- f-strings can contain function calls, method calls, arithmetic, and even nested f-strings — genuinely arbitrary expressions, not just simple value substitution.
- The `=` specifier (Python 3.8+) inside an f-string (`f"{value=}"`) prints both the expression's source text and its value — a small but genuinely useful debugging shortcut.

---

## ⚙️ How It Works

At the point Python encounters an f-string literal, it evaluates every `{expression}` inside it immediately, in the current scope, converts the result to its string representation (unless a format specifier says otherwise), and splices it into the surrounding text — this all happens at the exact moment the f-string itself is evaluated, not at some later "template rendering" step the way some other languages' templating systems work. This is precisely why an f-string can contain a function call or a calculation and always reflect the current values at that point in execution, not stale ones from when the string literal was first written.

```text
name = "Alice"
age = 30
f"{name} is {age} years old"          # "Alice is 30 years old"
f"{name} will be {age + 1} next year"  # "Alice will be 31 next year" — a real expression, evaluated live
```

---

## 💻 Examples

```python
name = "Alice"
age = 30

# Basic interpolation
print(f"Hello, {name}! You are {age} years old.")

# Arbitrary expressions inside {}
print(f"Next year you'll be {age + 1}.")
print(f"Name in caps: {name.upper()}")

# Format specifiers
price = 19.999
print(f"${price:.2f}")           # $20.00 — rounds to 2 decimal places
print(f"{1000000:,}")             # 1,000,000 — thousands separator
print(f"{'hi':>10}")               # "        hi" — right-aligned, width 10
print(f"{'hi':<10}|")               # "hi        |" — left-aligned, width 10
print(f"{3.14159:.1%}")              # 314.2% — percentage formatting

# Debugging shortcut (Python 3.8+)
x = 42
print(f"{x=}")                       # "x=42" — shows BOTH the expression and its value

# Multi-line f-strings
report = (
    f"Name: {name}\n"
    f"Age: {age}\n"
)

# Legacy styles — still commonly seen in existing code
"{} is {}".format(name, age)         # .format() method
"%s is %d" % (name, age)              # old %-style formatting
```

---

## 🚀 Real World Applications

- Building readable, dynamic strings for output, logging, and user-facing messages
- Formatting numbers for display (currency, percentages, thousands separators) directly inline without separate formatting calls
- Using the `{x=}` debug specifier for quick, ad-hoc print-debugging without manually writing out variable names as separate string literals
- Reading and maintaining older codebases that still use `.format()` or `%`-style formatting, recognizing both as functionally equivalent to modern f-strings

---

## ⚖️ Advantages

- f-strings are generally the most readable formatting option, since the variable/expression appears directly where it's used rather than in a separate argument list.
- f-strings are also the fastest of Python's string formatting mechanisms at runtime, since the expression evaluation is compiled directly into the bytecode.
- Format specifiers provide a compact, consistent mini-language for common presentation needs (decimals, padding, alignment, percentages) without needing separate formatting function calls.

---

## ⚠️ Limitations

- f-strings require Python 3.6+ — code that needs to support older Python versions must fall back to `.format()` or `%`-style formatting.
- Because f-strings can embed arbitrary expressions, overly complex logic crammed inside `{}` can hurt readability — a well-named variable computed just before the f-string is often clearer than a complicated inline expression.
- f-strings cannot easily be used as a reusable "template" defined once and filled in with different values later — `.format()` is sometimes still preferable for genuinely reusable string templates.

---

## 🚨 Common Mistakes

- Forgetting the `f` prefix, resulting in a plain string containing the literal, unevaluated `{name}` text instead of the interpolated value.
- Confusing `.2f` (fixed 2 decimal places) with `,` (thousands separator) format specifiers, or forgetting they can be combined (`f"{value:,.2f}"`).
- Cramming genuinely complex, hard-to-read logic directly inside an f-string's braces instead of computing it in a clearly named variable beforehand.
- Assuming f-strings are safe for building SQL queries or shell commands from untrusted input — string interpolation of any kind (including f-strings) is not a substitute for proper parameterized queries or escaping when handling untrusted data.

---

## 📖 Further Reading

- Python documentation: "The Python Tutorial" — Section 7.1, "Fancier Output Formatting"
- PEP 498: "Literal String Interpolation" (the f-string proposal)

---

## 💡 Wisdom from Mímir

Default to f-strings for essentially all new string-building code — they're more readable, faster, and support everything `.format()` and `%`-formatting do, plus arbitrary live expressions. The only reason to reach for the older styles is compatibility with genuinely old Python versions or an existing reusable template pattern already built around `.format()`.

---

## 🔗 Related Notes

- [[Variables, Types, and Dynamic Typing]]
- [[Working with JSON in Python]]
- [[Python Codex]]
