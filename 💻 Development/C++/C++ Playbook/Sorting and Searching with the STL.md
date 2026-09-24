---
type: cpp-pattern
status: published
created: 2026-09-23
updated: 2026-09-23
technology: C++
difficulty: Beginner
tags:
  - stl-algorithms
  - sorting
  - searching
aliases:
  - std::sort
  - std::find
publish: true
permalink: cpp/sorting-and-searching-with-the-stl
---

# <span class="rune">ᚲ</span> Sorting and Searching with the STL

> *Hand-writing a sort algorithm is a great learning exercise exactly once. After that, `std::sort` is faster, more tested, and more correct than anything you'd write from scratch in the same five minutes.*

---

## 🎯 Problem

A collection of values needs to be sorted, or a specific value needs to be found within it — without hand-writing a sorting or searching algorithm from scratch every time.

More generally, this pattern answers:
> How do I sort a container and search for values in it using the standard library, instead of writing the algorithm myself?

---

## 🤔 Mental Model

Treat `std::sort` and `std::find` the same way you'd treat `Collections.sort()` and a `for` loop with `.equals()` in Java, or `sorted()`/`in` in Python — a well-tested, optimized standard tool that almost always beats a hand-rolled version, both in correctness and performance. The only genuinely new piece coming from those languages is that these algorithms operate through [[The Standard Template Library Overview|iterators]] rather than being called directly as a method on the container.

---

## 🧠 Why This Pattern Works

`std::sort(begin, end)` and `std::find(begin, end, value)` both live in `<algorithm>` and operate generically on any range defined by a pair of iterators — meaning the exact same function calls work unmodified on a `std::vector`, a `std::array`, or a plain array's begin/end pointers, as covered generally in [[The Standard Template Library Overview]]. `std::sort` uses an efficient, well-tuned sorting algorithm (typically a hybrid like introsort) under the hood, and `std::find` performs a straightforward linear search, returning an iterator to the found element (or the range's `end()` if nothing matched).

---

## 💻 C++ Solution

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main() {
    vector<int> nums = {5, 2, 8, 1, 9, 3};

    // Sorting
    sort(nums.begin(), nums.end());
    for (int n : nums) cout << n << " ";   // 1 2 3 5 8 9
    cout << endl;

    // Sorting in descending order
    sort(nums.begin(), nums.end(), greater<int>());
    for (int n : nums) cout << n << " ";   // 9 8 5 3 2 1
    cout << endl;

    // Searching
    auto it = find(nums.begin(), nums.end(), 8);
    if (it != nums.end()) {
        cout << "Found at position: " << (it - nums.begin()) << endl;
    } else {
        cout << "Not found" << endl;
    }

    return 0;
}
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Sort a range using iterators

```cpp
sort(nums.begin(), nums.end());
```

Sorts in-place, ascending by default, using `<` to compare elements.

### Step 2 — Customize the sort order with a comparator

```cpp
sort(nums.begin(), nums.end(), greater<int>());
```

`greater<int>()` reverses the comparison, producing descending order — a custom lambda can be used the same way for more complex sort criteria.

### Step 3 — Search and check the result against end()

```cpp
auto it = find(nums.begin(), nums.end(), 8);
if (it != nums.end()) { ... }
```

`std::find` returns an iterator — comparing it against `.end()` is how you check "was it actually found," exactly mirroring the `String.indexOf() == -1`-style check in Java, just expressed through iterators instead of an index.

---

## 🚀 Common Use Cases

- Sorting a collection of values before displaying or processing them in order
- Searching for a specific value's presence (and position) within a container
- Sorting custom objects by a specific field using a lambda comparator
- Using `std::binary_search` for a much faster (`O(log n)`) presence check on an already-sorted range, instead of `std::find`'s `O(n)` linear scan

---

## ⚖️ Alternatives

### Sorting custom objects with a lambda comparator

```cpp
struct Person { string name; int age; };
vector<Person> people = {{"Bob", 25}, {"Alice", 30}};

sort(people.begin(), people.end(), [](const Person& a, const Person& b) {
    return a.age < b.age;      // sort by age, ascending
});
```

### Binary search on an already-sorted range (much faster than std::find)

```cpp
bool found = binary_search(nums.begin(), nums.end(), 8);   // requires nums to ALREADY be sorted
```

### Counting occurrences

```cpp
int count = count(nums.begin(), nums.end(), 5);   // how many times does 5 appear?
```

---

## 🚨 Common Mistakes

- Using `std::binary_search`/`std::lower_bound` on a range that isn't actually sorted — these algorithms assume sorted input and produce meaningless results (not an error) if that assumption is violated.
- Forgetting `std::find`'s linear search is `O(n)` — for frequent lookups on a large, static dataset, sorting once and using `binary_search` (or switching to `std::set`/`std::unordered_set` entirely) is far more efficient.
- Writing a custom comparator that isn't a strict weak ordering (e.g., using `<=` instead of `<`), which can cause `std::sort` to behave unpredictably or even crash in some implementations.
- Forgetting to `#include <algorithm>` — these functions live there, not in `<vector>` or `<iostream>`, and omitting it causes a compiler error that doesn't always obviously point at the missing include.

---

## 💡 Wisdom from Mímir

The instinct to hand-write a sort or search loop is worth having once, for the learning value — but in real code, reach for `std::sort`/`std::find`/`std::binary_search` every time afterward. They're better tested, better optimized, and communicate intent to any other reader far more clearly than an equivalent hand-rolled loop ever could.

---

## 🔗 Related Notes

- [[The Standard Template Library Overview]]
- [[Range-Based For Loops and Iterators]]
- [[C++ Codex]]
