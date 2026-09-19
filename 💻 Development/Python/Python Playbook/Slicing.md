---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Python
difficulty: Beginner
tags:
  - python-fundamentals
  - slicing
  - sequences
aliases:
  - Python Slice Notation
  - Negative Indexing Python
publish: true
permalink: python/slicing
---

# <span class="rune">ᛟ</span> Slicing

> *`[start:stop:step]` never throws an out-of-bounds error, no matter how far past the actual sequence you reach — it just quietly gives you as much as genuinely exists. Indexing a single element has no such mercy.*

---

## 🎯 Purpose

Slicing (`sequence[start:stop:step]`) extracts a sub-portion of any sequence type — strings, lists, and tuples all support it identically — using a compact, consistent syntax. It's one of Python's most distinctive and heavily used features, and understanding its exact rules (especially that the `stop` index is *exclusive*, and that out-of-range slices don't error) prevents a whole category of off-by-one confusion.

---

## 🧠 Key Ideas

- `sequence[start:stop]` returns elements from index `start` up to (but **not including**) index `stop` — the stop index is always exclusive.
- Omitting `start` defaults to the beginning (`0`); omitting `stop` defaults to the end of the sequence; `sequence[:]` returns a full copy of the entire sequence.
- **Negative indices** count from the end: `-1` is the last element, `-2` the second-to-last, and this works inside slices too (`sequence[-3:]` gets the last three elements).
- The optional third parameter, `step`, controls the interval between selected elements: `sequence[::2]` takes every second element; `sequence[::-1]` reverses the entire sequence.
- Unlike direct indexing (`sequence[10]`, which raises `IndexError` if out of range), **slicing never raises an error for an out-of-range boundary** — it simply returns as much as actually exists, even an empty sequence if nothing matches at all.

---

## ⚙️ How It Works

A slice is really shorthand for "start at this position, stop just before that position, taking every `step`-th element along the way" — internally, Python clamps `start` and `stop` to the sequence's actual bounds before extracting anything, which is exactly why `my_list[2:1000]` on a 5-element list doesn't error; it simply clamps `1000` down to the list's actual length and returns whatever's left from index 2 onward. Negative indices are resolved to their equivalent positive position (`-1` becomes `len(sequence) - 1`) before the same slicing logic applies, so negative and positive indices can even be mixed within the same slice.

```text
letters = ['a', 'b', 'c', 'd', 'e']
#           0    1    2    3    4
#          -5   -4   -3   -2   -1

letters[1:3]     # ['b', 'c']         — stop index (3) is EXCLUSIVE
letters[1:100]   # ['b', 'c', 'd', 'e'] — clamped, no error
letters[-2:]     # ['d', 'e']          — last two, via negative indexing
letters[::-1]    # ['e', 'd', 'c', 'b', 'a'] — reversed, via a negative step
```

---

## 💻 Examples

```python
letters = ['a', 'b', 'c', 'd', 'e']

letters[1:3]      # ['b', 'c']
letters[:3]        # ['a', 'b', 'c']  — from the start
letters[2:]         # ['c', 'd', 'e']  — to the end
letters[:]           # ['a', 'b', 'c', 'd', 'e']  — a full COPY

letters[-1]           # 'e' — the last element
letters[-3:]           # ['c', 'd', 'e']  — last three
letters[:-2]            # ['a', 'b', 'c']  — everything except the last two

letters[::2]              # ['a', 'c', 'e']  — every second element
letters[::-1]               # ['e', 'd', 'c', 'b', 'a']  — reversed, the idiomatic way

letters[10:20]                # []  — completely out of range, but NO ERROR — just empty
# letters[10]                 # IndexError — direct indexing DOES error out of range

# Slicing works identically on strings and tuples
s = "Hello, World!"
s[7:12]          # "World"
s[::-1]           # "!dlroW ,olleH"  — reversed string, idiomatic trick

t = (10, 20, 30, 40, 50)
t[1:4]              # (20, 30, 40)

# Modifying a list via slice assignment — lists ONLY, not strings/tuples (immutable)
nums = [1, 2, 3, 4, 5]
nums[1:3] = [20, 30, 40]   # replaces indices 1-2 with THREE new values — length can change
print(nums)                 # [1, 20, 30, 40, 4, 5]
```

---

## 🚀 Real World Applications

- Extracting a substring, a sub-list, or a portion of any sequence with a compact, expressive syntax
- Reversing a sequence idiomatically with `[::-1]` instead of a separate reverse function or manual loop
- Taking the "last N" or "first N" elements of a sequence cleanly with negative or simple positive slicing
- Downsampling or skipping elements at a regular interval with a non-default `step` value

---

## ⚖️ Advantages

- Extremely compact and expressive compared to manually looping to extract a sub-range.
- Never raises an out-of-range error, making it safe and convenient for extracting "up to N elements" without first checking the sequence's actual length.
- Works identically across strings, lists, and tuples, so the same mental model transfers directly between sequence types.

---

## ⚠️ Limitations

- The exclusive `stop` index is a very common source of off-by-one confusion, especially for anyone expecting inclusive ranges by default.
- Slicing always creates a **new** sequence (a copy) — for very large sequences, taking many slices can have real memory and performance costs compared to working with indices/views directly.
- Slice assignment (replacing a range of a list with new values) can change the list's length in ways that are easy to get wrong if not fully understood.

---

## 🚨 Common Mistakes

- Expecting `sequence[1:3]` to include index `3` — the stop index is always exclusive; only indices `1` and `2` are actually returned.
- Assuming a slice out of range will raise an error the same way direct indexing does — slicing is forgiving by design and never raises `IndexError`.
- Forgetting `[:]` creates a shallow copy — for a list of mutable objects, the copy shares references to the same inner objects, not independent deep copies of them.
- Using a manual loop with indices to reverse or extract a sub-sequence when a slice (`[::-1]`, `[start:stop]`) would be both shorter and more idiomatic.

---

## 📖 Further Reading

- Python documentation: "The Python Tutorial" — Section 3.1.2, "Strings" (slicing introduced here, applies identically to lists/tuples)
- Python documentation: "Common Sequence Operations"

---

## 💡 Wisdom from Mímir

Read `sequence[start:stop]` out loud as "starting at `start`, stop *just before* `stop`" every time it's unclear — internalizing that the stop boundary is a fence you approach but never touch resolves the overwhelming majority of slicing confusion immediately.

---

## 🔗 Related Notes

- [[Lists, Tuples, and Sets]]
- [[String Formatting and f-strings]]
- [[Python Codex]]
