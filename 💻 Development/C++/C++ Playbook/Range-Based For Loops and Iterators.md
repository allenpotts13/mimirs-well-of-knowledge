---
type: cpp-pattern
status: published
created: 2026-09-23
updated: 2026-09-23
technology: C++
difficulty: Beginner
tags:
  - loops
  - iterators
  - range-based-for
aliases:
  - "for (auto x : container)"
  - Range-Based For Loop C++
publish: true
permalink: cpp/range-based-for-loops-and-iterators
---

# <span class="rune">ᚲ</span> Range-Based For Loops and Iterators

> *`for (int x : nums)` looks exactly like Java's enhanced for-each, and once compiled, it basically becomes one — a thin layer of syntax sugar sitting directly on top of the same begin()/end() iterator mechanism every STL algorithm already uses.*

---

## 🎯 Problem

Iterating over every element of a container to read or modify it, without manually managing an index or an explicit iterator, the same convenience Java's enhanced for-each and Python's `for x in list` already provide.

More generally, this pattern answers:
> How do I iterate over a container's elements cleanly, and when do I still need an explicit iterator instead?

---

## 🤔 Mental Model

The range-based for loop is C++'s direct equivalent of Java's `for (Type item : collection)` — same purpose, nearly identical syntax. The one genuinely new piece is `auto&` versus `auto`: whether you're working with a *copy* of each element or a *reference* to the actual element inside the container, which decides whether modifications during the loop actually stick.

---

## 🧠 Why This Pattern Works

`for (auto x : container)` is compiler sugar that expands to essentially the same iterator-based loop `std::sort`/`std::find` use internally — calling `.begin()`, comparing against `.end()`, and incrementing on each pass, exactly as covered in [[The Standard Template Library Overview]]. The critical detail is what `auto` actually binds to: plain `auto` copies each element (so modifying it inside the loop has no effect on the original container), while `auto&` binds a genuine reference to the original element (so modifications *do* persist), and `const auto&` gives a read-only reference — avoiding a copy for performance without allowing accidental modification.

---

## 💻 C++ Solution

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    vector<int> nums = {1, 2, 3, 4, 5};

    // Read-only iteration — auto copies each value (fine for small types like int)
    for (int n : nums) {
        cout << n << " ";
    }
    cout << endl;

    // Modifying elements — REQUIRES a reference, or changes don't stick
    for (int& n : nums) {
        n *= 2;              // modifies the ACTUAL element in nums
    }
    for (int n : nums) cout << n << " ";   // 2 4 6 8 10 — the modification stuck

    // const reference — avoids copying (efficient for large objects), but read-only
    for (const string& s : vector<string>{"a", "b", "c"}) {
        cout << s << " ";
    }

    return 0;
}
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Use plain value iteration for read-only access to small types

```cpp
for (int n : nums) { cout << n; }
```

Fine for small, cheap-to-copy types like `int`/`double` where the copy cost is negligible.

### Step 2 — Use a reference to modify elements in place

```cpp
for (int& n : nums) { n *= 2; }
```

Without the `&`, `n` would be a copy — doubling it would have zero effect on the actual `nums` container.

### Step 3 — Use a const reference for large objects you only need to read

```cpp
for (const string& s : words) { cout << s; }
```

Avoids copying an expensive-to-copy object (like a large `std::string`) on every iteration, while still preventing accidental modification.

---

## 🚀 Common Use Cases

- Iterating over any STL container's elements for display, processing, or aggregation
- Modifying every element in a container in place using a reference-based loop
- Avoiding unnecessary copies of large objects during read-only iteration with `const auto&`
- Falling back to an explicit iterator (rather than range-based for) when you need the *position* itself, not just the value — like removing a specific element mid-iteration

---

## ⚖️ Alternatives

### Explicit iterator syntax (what range-based for is sugar over)

```cpp
for (vector<int>::iterator it = nums.begin(); it != nums.end(); ++it) {
    cout << *it << " ";     // must DEREFERENCE the iterator to get the value
}
```

Needed when you require the iterator itself — for example, to erase an element mid-loop, which range-based for cannot express directly.

### Index-based loop, when the position itself matters

```cpp
for (size_t i = 0; i < nums.size(); i++) {
    cout << "Index " << i << ": " << nums[i] << endl;
}
```

Use this when you genuinely need the index (not just the value) — range-based for gives you the value only, with no built-in position tracking.

---

## 🚨 Common Mistakes

- Using plain `auto` (copying) when modification was actually intended, then being confused why changes inside the loop don't persist in the original container.
- Using `auto&` (or plain `auto`, copying a large object) for expensive-to-copy types unnecessarily, when `const auto&` would avoid the copy entirely for read-only access.
- Trying to erase elements from a container while iterating it with a range-based for loop — this invalidates the underlying iterator mid-loop and is undefined behavior; an explicit iterator with the container's own `.erase()` return value is required instead.
- Forgetting that a range-based for loop copies (or references) the *container's* elements each iteration, not a snapshot — modifying the container's size while iterating range-based-for is not supported safely.

---

## 💡 Wisdom from Mímir

Ask one question before writing `for (auto x : container)`: do I need to modify these elements? If yes, `auto&`. If no, but the elements are expensive to copy, `const auto&`. Only reach for plain `auto` when the elements are cheap (an `int`, a small `struct`) and modification genuinely isn't needed — getting this one choice right by habit avoids a whole category of "why didn't my change stick" bugs.

---

## 🔗 Related Notes

- [[The Standard Template Library Overview]]
- [[Sorting and Searching with the STL]]
- [[C++ Codex]]
