---
type: cpp-pattern
status: published
created: 2026-09-23
updated: 2026-09-23
technology: C++
difficulty: Beginner
tags:
  - arrays
  - vector
  - stl-containers
aliases:
  - std::vector vs Array
  - When to Use vector
publish: true
permalink: cpp/arrays-vs-std-vector
---

# <span class="rune">ᚲ</span> Arrays vs std::vector

> *A raw C-style array doesn't know its own size — you have to remember it separately, everywhere. A std::vector always knows, because it's an object that carries that information with it, not just a bare pointer to the first element.*

---

## 🎯 Problem

A program needs to store a list of values, but the exact count isn't known until runtime, or needs to grow. A fixed-size raw array can't do either safely.

More generally, this pattern answers:
> When should I use a raw C-style array versus `std::vector`, and what does `std::vector` actually solve?

---

## 🤔 Mental Model

A raw array is like a row of labeled parking spots painted onto pavement — fixed, permanent, and the row itself has no idea how many spots it has; you have to remember that separately. `std::vector` is a parking garage with an attendant: it tracks its own capacity, can add more levels automatically as needed, and always knows exactly how many cars are currently parked, because that bookkeeping is built into the garage itself, not left for you to track on the side.

---

## 🧠 Why This Pattern Works

A raw array (`int arr[5]`) is just a fixed block of memory with no self-awareness — there's no `.size()`, no bounds checking, and passing it to a function silently decays it to a bare pointer, losing the size information entirely. `std::vector<int>` wraps that same underlying contiguous memory in a class that tracks its own size and capacity, automatically reallocates and grows as elements are added, and provides `.size()`, `.push_back()`, bounds-checked access via `.at()`, and full support for the [[The Standard Template Library Overview|STL's]] algorithms and iterators — essentially everything Java's `ArrayList` provides, built the same way.

---

## 💻 C++ Solution

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    // Raw array — fixed size, known at compile time, no size tracking
    int rawArr[5] = {1, 2, 3, 4, 5};
    int size = 5;   // must be tracked SEPARATELY — the array itself doesn't know

    // std::vector — dynamic, self-tracking, grows as needed
    vector<int> vec = {1, 2, 3, 4, 5};
    vec.push_back(6);              // grows automatically — no manual resizing needed
    cout << vec.size() << endl;      // 6 — vector KNOWS its own size

    return 0;
}
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Recognize when a fixed size is genuinely known and won't change

```cpp
int scores[10];   // fine — a known, fixed count that will never need to grow
```

Raw arrays are still appropriate for genuinely fixed-size, performance-critical, low-level cases — they're not obsolete, just narrower in scope than they might first appear.

### Step 2 — Default to std::vector for anything that might grow or whose size isn't known upfront

```cpp
vector<int> scores;               // starts empty
scores.push_back(95);               // grows to hold it
scores.push_back(88);                 // grows again
```

### Step 3 — Use bounds-checked access when safety matters more than raw speed

```cpp
vec.at(10);    // throws std::out_of_range if index 10 doesn't exist
vec[10];         // UNDEFINED BEHAVIOR if index 10 doesn't exist — no check at all, just like a raw array
```

---

## 🚀 Common Use Cases

- Reading an unknown number of values from user input or a file into a growing collection
- Passing a collection to a function without losing size information the way a raw array parameter would
- Using `std::vector` as the default "give me a list" container, exactly the role `ArrayList` plays in Java
- Reaching for a raw array specifically in low-level, size-fixed-and-known contexts (like a small, fixed lookup table)

---

## ⚖️ Alternatives

### std::array — a fixed-size container with vector-like convenience

```cpp
#include <array>
array<int, 5> arr = {1, 2, 3, 4, 5};   // size is FIXED (5) but the object still knows it, unlike a raw array
cout << arr.size() << endl;               // 5 — works, unlike a raw array
```

`std::array` is the modern choice when the size genuinely is fixed and known at compile time, but you still want the safety and convenience methods (`.size()`, `.at()`, iterator support) a raw array lacks.

### Reserving capacity upfront to avoid repeated reallocation

```cpp
vector<int> vec;
vec.reserve(1000);   // pre-allocates space for 1000 elements, avoiding multiple reallocations as it grows
```

---

## ⚠️ Performance Considerations

- `std::vector`'s automatic growth occasionally triggers a full reallocation (copying every existing element to a new, larger block) — using `.reserve()` upfront when the approximate final size is known avoids repeated reallocation costs.
- Raw arrays have zero overhead beyond the data itself; `std::vector` has a small amount of bookkeeping overhead (size, capacity) — negligible for nearly all real programs, but worth knowing about in genuinely performance-critical, low-level code.
- `.at()`'s bounds checking has a small runtime cost compared to `[]`'s no-check access — reach for `.at()` when input might be invalid, `[]` when the index is already known-safe.

---

## 🚨 Common Mistakes

- Passing a raw array to a function by pointer and losing its size information entirely — the function has no way to know how many elements it's actually working with unless the size is passed separately.
- Using `[]` instead of `.at()` on a `std::vector` when the index isn't guaranteed valid, silently triggering undefined behavior instead of getting a clear, catchable exception.
- Assuming `std::vector` always has the same performance characteristics as a raw array — true for indexed access, but insertion/removal in the middle is `O(n)`, exactly the same tradeoff [[ArrayList vs LinkedList|Java's ArrayList]] has.
- Reflexively using raw arrays out of habit from a C-focused course, when `std::vector` (or `std::array` for a genuinely fixed size) is the safer, more idiomatic modern C++ default.

---

## 💡 Wisdom from Mímir

Default to `std::vector` the same way you'd default to `ArrayList` in Java — reach for a raw array (or `std::array`) only when you have a specific, deliberate reason: a genuinely fixed, compile-time-known size, or a low-level context where the tiny bit of vector overhead actually matters.

---

## 🔗 Related Notes

- [[The Standard Template Library Overview]]
- [[String Handling with std-string]]
- [[Range-Based For Loops and Iterators]]
- [[C++ Codex]]
