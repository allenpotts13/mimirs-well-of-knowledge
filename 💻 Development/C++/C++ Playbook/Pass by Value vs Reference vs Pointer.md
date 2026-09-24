---
type: cpp-pattern
status: published
created: 2026-09-23
updated: 2026-09-23
technology: C++
difficulty: Intermediate
tags:
  - functions
  - pass-by-reference
  - parameters
aliases:
  - Pass by Reference C++
  - Function Parameter Passing
publish: true
permalink: cpp/pass-by-value-vs-reference-vs-pointer
---

# <span class="rune">ᚲ</span> Pass by Value vs Reference vs Pointer

> *Java only ever gives you one of these three choices, and calls it "pass by value" even for objects — because what's actually being copied is the reference itself. C++ gives you all three, explicitly, and expects you to pick the right one on purpose.*

---

## 🎯 Problem

A function needs to either read data without modifying the caller's copy, or actually modify the caller's original variable — and choosing the wrong parameter-passing style either wastes performance on unnecessary copying, or fails to modify what the caller expected to change.

More generally, this pattern answers:
> Should a function parameter be passed by value, by reference, or by pointer — and what does each one actually guarantee?

---

## 🤔 Mental Model

**Pass by value** hands the function a photocopy — it can scribble on it all it wants, and the original document back at the caller is untouched. **Pass by reference** hands the function the actual original document, under a different name — anything written on it is really written on the original. **Pass by pointer** hands the function a note with the document's location written on it — the function has to explicitly "go to that location" (dereference) before reading or writing, but it's still ultimately working with the same original document.

---

## 🧠 Why This Pattern Works

Building directly on [[Pointers and References]]: passing by value copies the argument's actual data into the function's parameter, so [[Memory Management - Stack, Heap, new, and delete|the copy lives independently on the stack]] and any changes inside the function never touch the caller's original. Passing by reference (`Type&`) makes the parameter an alias for the caller's actual variable — no copy at all, and any modification inside the function directly modifies the original. Passing by pointer (`Type*`) is similar to reference in that no data is copied, but the function receives an address it must explicitly dereference (`*ptr`) to actually read or write the value, and — unlike a reference — the pointer itself can be `nullptr` or reassigned.

---

## 💻 C++ Solution

```cpp
#include <iostream>
using namespace std;

// Pass by VALUE — a copy, changes do NOT affect the caller's original
void incrementByValue(int x) {
    x++;
}

// Pass by REFERENCE — the actual variable, changes DO affect the caller's original
void incrementByReference(int& x) {
    x++;
}

// Pass by POINTER — an address; must dereference to modify, changes DO affect the caller's original
void incrementByPointer(int* x) {
    (*x)++;
}

int main() {
    int a = 5;
    incrementByValue(a);
    cout << a << endl;         // 5 — UNCHANGED, "a" was copied

    int b = 5;
    incrementByReference(b);
    cout << b << endl;           // 6 — CHANGED, "b" itself was modified

    int c = 5;
    incrementByPointer(&c);        // must pass the ADDRESS explicitly
    cout << c << endl;               // 6 — CHANGED, via the pointer

    return 0;
}
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Default to pass-by-value for small types you don't need to modify

```cpp
void printValue(int x) { cout << x; }
```

Copying a small type like `int` is essentially free — no reason to reach for a reference here.

### Step 2 — Use `const Type&` for larger objects you only need to read

```cpp
void printName(const string& name) { cout << name; }
```

Avoids copying a potentially large object, while `const` prevents the function from accidentally modifying the caller's original — the best of both worlds for read-only access to non-trivial types.

### Step 3 — Use `Type&` (no const) specifically when the function needs to modify the caller's original

```cpp
void doubleValue(int& x) { x *= 2; }
```

### Step 4 — Use a pointer specifically when "no value at all" (nullptr) needs to be representable, or when reassignment is needed

```cpp
void processIfPresent(int* x) {
    if (x != nullptr) { /* ... */ }
}
```

---

## 🚀 Common Use Cases

- Passing small built-in types (`int`, `double`, `bool`) by value, since copying them costs essentially nothing
- Passing large objects (`std::string`, `std::vector`, custom classes) by `const&` when the function only needs to read them
- Passing by non-const reference specifically when a function is meant to modify the caller's variable directly (an alternative to returning multiple values, covered in [[Returning Multiple Values]])
- Passing by pointer when the argument might legitimately be "nothing" (`nullptr`), which a reference can never represent

---

## ⚖️ Alternatives

### Returning a new value instead of modifying a parameter

```cpp
int doubled(int x) { return x * 2; }   // often clearer than modifying a reference parameter, when a single result suffices
int result = doubled(5);
```

Frequently a cleaner design than pass-by-reference when a function only needs to compute and hand back one new value, rather than genuinely needing to mutate the caller's variable in place.

---

## ⚠️ Performance Considerations

- Passing a large object (a big `std::string`, a `std::vector` with thousands of elements) by value triggers a full copy on every call — for read-only access, `const&` avoids this cost entirely.
- Passing small built-in types by reference instead of by value provides no benefit and can occasionally be slightly *slower*, since dereferencing a reference/pointer has its own tiny overhead compared to a value already sitting directly in a register.

---

## 🚨 Common Mistakes

- Passing a large object by value out of habit, unknowingly copying it on every single function call — a genuine, measurable performance cost for anything beyond small built-in types.
- Forgetting `const` on a reference parameter that's only meant to be read, leaving the function able to accidentally modify the caller's original when that was never the intent.
- Using a pointer parameter and forgetting to check for `nullptr` before dereferencing it, risking undefined behavior the moment a caller passes one in.
- Passing by reference when the actual intent was just to avoid a copy for reading — `const Type&` communicates "read-only, no copy" far more clearly than a non-const reference that could technically also modify the argument.

---

## 💡 Wisdom from Mímir

When deciding how to pass a parameter, ask two questions in order: does the function need to modify the caller's original? If no, prefer `const Type&` for anything beyond a small built-in type, or plain value for something small and cheap to copy. If yes, use `Type&` — and reach for a pointer specifically only when "no value at all" genuinely needs to be representable.

---

## 🔗 Related Notes

- [[Pointers and References]]
- [[Returning Multiple Values]]
- [[C++ Codex]]
