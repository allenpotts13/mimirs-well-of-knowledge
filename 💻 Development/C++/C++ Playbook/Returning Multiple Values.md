---
type: cpp-pattern
status: published
created: 2026-09-23
updated: 2026-09-23
technology: C++
difficulty: Intermediate
tags:
  - functions
  - std-pair
  - std-tuple
aliases:
  - std::pair Return
  - std::tuple Return
  - Multiple Return Values C++
publish: true
permalink: cpp/returning-multiple-values
---

# <span class="rune">ᚲ</span> Returning Multiple Values

> *A C++ function still only ever returns exactly one thing — `std::pair` and `std::tuple` don't change that rule, they just make "one thing" flexible enough to bundle several values together.*

---

## 🎯 Problem

A function logically needs to hand back more than one result — a minimum and a maximum, a success flag alongside a computed value — but a C++ function's `return` statement only accepts a single value.

More generally, this pattern answers:
> How do I return more than one value from a C++ function, given that a function can only formally return one thing?

---

## 🤔 Mental Model

Java and Python both let you casually return a small array, a `List`, or a tuple without much ceremony. C++ takes a slightly more explicit route: `std::pair` bundles exactly two values into a single object; `std::tuple` generalizes that to any number; or, as covered in [[Pass by Value vs Reference vs Pointer]], out-parameters let the function write results directly into variables the caller already provided. All three are really just different answers to the same constraint — a function returns one thing, so "multiple values" has to be packaged as one thing first.

---

## 🧠 Why This Pattern Works

`std::pair<T1, T2>` is a simple templated struct holding exactly two members, `.first` and `.second` — returning one lets a function package two logically related values (a min and a max, a key and a value) as a single returned object. `std::tuple<T1, T2, T3, ...>` generalizes this to any number of values, accessed via `std::get<index>(t)` or, more readably, unpacked directly with structured bindings (C++17+). Both are genuinely single return values from the compiler's perspective — the "multiple values" framing is purely at the level of how you, the programmer, choose to interpret and unpack that one returned object afterward.

---

## 💻 C++ Solution

```cpp
#include <iostream>
#include <utility>   // std::pair
#include <tuple>       // std::tuple
#include <vector>
using namespace std;

// Returning two values with std::pair
pair<int, int> getMinMax(const vector<int>& nums) {
    int lo = nums[0], hi = nums[0];
    for (int n : nums) {
        if (n < lo) lo = n;
        if (n > hi) hi = n;
    }
    return {lo, hi};   // constructs a pair implicitly
}

// Returning three+ values with std::tuple
tuple<int, int, double> getStats(const vector<int>& nums) {
    int sum = 0;
    for (int n : nums) sum += n;
    int count = nums.size();
    double average = static_cast<double>(sum) / count;
    return {sum, count, average};
}

int main() {
    vector<int> data = {4, 2, 8, 1, 9, 5};

    // Using std::pair
    pair<int, int> result = getMinMax(data);
    cout << "Min: " << result.first << ", Max: " << result.second << endl;

    // Structured bindings (C++17+) — cleaner unpacking, works for pair AND tuple
    auto [lo, hi] = getMinMax(data);
    cout << "Min: " << lo << ", Max: " << hi << endl;

    auto [sum, count, avg] = getStats(data);
    cout << "Sum: " << sum << ", Count: " << count << ", Avg: " << avg << endl;

    return 0;
}
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Choose pair for exactly two values, tuple for more

```cpp
pair<int, int> getMinMax(...) { return {lo, hi}; }
tuple<int, int, double> getStats(...) { return {sum, count, average}; }
```

### Step 2 — Unpack the result with structured bindings (modern, C++17+)

```cpp
auto [lo, hi] = getMinMax(data);
```

Gives each returned value its own clearly-named local variable directly, instead of accessing `.first`/`.second` or `std::get<0>(...)` — much more readable.

### Step 3 — Fall back to .first/.second or std::get<N> on older C++ standards

```cpp
pair<int, int> result = getMinMax(data);
cout << result.first << ", " << result.second;

tuple<int, int, double> stats = getStats(data);
cout << get<0>(stats) << ", " << get<1>(stats) << ", " << get<2>(stats);
```

---

## 🚀 Common Use Cases

- Returning a computed minimum and maximum together from a single pass over data
- Returning a success/failure flag alongside a computed value, instead of using a sentinel value or an out-parameter
- Returning several related statistics (sum, count, average) from one aggregation function
- Preferring `std::tuple`/structured bindings over multiple out-parameters (see [[Pass by Value vs Reference vs Pointer]]) when the values being returned don't have an obvious "owner" variable already in the caller

---

## ⚖️ Alternatives

### Out-parameters (pass by reference) instead of a packaged return value

```cpp
void getMinMax(const vector<int>& nums, int& outMin, int& outMax) {
    outMin = outMax = nums[0];
    for (int n : nums) {
        if (n < outMin) outMin = n;
        if (n > outMax) outMax = n;
    }
}

int lo, hi;
getMinMax(data, lo, hi);   // caller's variables are modified directly
```

An older, still-common style — writes results directly into caller-provided variables via [[Pass by Value vs Reference vs Pointer|reference parameters]] instead of packaging them into a returned object. Some style guides prefer `std::tuple`/`std::pair` for readability; others prefer explicit out-parameters for clarity about what's being modified.

### A small custom struct, for self-documenting field names

```cpp
struct MinMaxResult { int min; int max; };
MinMaxResult getMinMax(const vector<int>& nums) { /* ... */ return {lo, hi}; }

MinMaxResult r = getMinMax(data);
cout << r.min << ", " << r.max;    // "min"/"max" are far more self-documenting than .first/.second
```

Often the clearest option when the returned values have meaningful names — `.first`/`.second` (or `std::get<0>`) don't communicate intent the way named struct fields do.

---

## 🚨 Common Mistakes

- Using `std::pair`'s `.first`/`.second` for more than two conceptually-related values, forcing an awkward, hard-to-read nesting instead of switching to `std::tuple` or a proper named struct.
- Forgetting structured bindings require C++17 or later — using `auto [a, b] = ...` on an older standard produces a compiler error about an unsupported feature.
- Choosing `std::tuple` with `std::get<0>`/`std::get<1>` access when a small custom struct with named fields would communicate the returned values' meaning far more clearly to future readers.
- Reaching for out-parameters (reference parameters) purely out of habit when a packaged return value (pair/tuple/struct) would make the function's actual return contract clearer at the call site.

---

## 💡 Wisdom from Mímir

Once a function needs to hand back more than a couple of loosely-related values, stop reaching for `std::pair`/`std::tuple` and just write a small named struct instead — `result.min`/`result.max` will always be clearer to the next reader than `result.first`/`result.second`, and it costs almost nothing to define.

---

## 🔗 Related Notes

- [[Pass by Value vs Reference vs Pointer]]
- [[Structs vs Classes in C++]]
- [[C++ Codex]]
