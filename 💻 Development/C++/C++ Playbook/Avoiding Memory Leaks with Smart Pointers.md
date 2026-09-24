---
type: cpp-pattern
status: published
created: 2026-09-23
updated: 2026-09-23
technology: C++
difficulty: Intermediate
tags:
  - memory-management
  - smart-pointers
  - unique-ptr
aliases:
  - Preventing Memory Leaks C++
  - unique_ptr Pattern
publish: true
permalink: cpp/avoiding-memory-leaks-with-smart-pointers
---

# <span class="rune">ᚲ</span> Avoiding Memory Leaks with Smart Pointers

> *The fastest way to guarantee you never forget a `delete` is to never write one at all — hand every heap allocation to a smart pointer immediately, and let its destructor do the remembering for you.*

---

## 🎯 Problem

A program allocates heap memory with `new`, but an early return, an exception, or simple human error means the matching `delete` never runs — a leak that quietly accumulates every time that code path executes.

More generally, this pattern answers:
> How do I structure heap allocation so a leak becomes essentially impossible, instead of relying on carefully remembering every `delete`?

---

## 🤔 Mental Model

Every raw `new` without an immediately-attached smart pointer is an IOU you're trusting yourself to pay later, on every possible exit path from the function. Wrapping it in a [[Smart Pointers - unique_ptr and shared_ptr|smart pointer]] the instant it's allocated turns that IOU into cash paid up front — the debt (the eventual `delete`) is already handled, automatically, no matter how the function actually exits.

---

## 🧠 Why This Pattern Works

`std::unique_ptr`/`std::shared_ptr` are [[RAII - Resource Acquisition Is Initialization|RAII]] wrappers — their destructors call `delete` automatically the instant they go out of scope, on every possible exit path including an early `return` or an exception unwinding the stack. The practical discipline this enables: never let a raw `new`'s result sit unattended, even briefly — wrap it in `make_unique`/`make_shared` in the very same statement that allocates it, so there's never a window where a bare, unmanaged pointer to heap memory exists at all.

---

## 💻 C++ Solution

```cpp
#include <iostream>
#include <memory>
using namespace std;

class Resource {
public:
    Resource() { cout << "Resource acquired" << endl; }
    ~Resource() { cout << "Resource released" << endl; }
    void use() { cout << "Using resource" << endl; }
};

// LEAK-PRONE — raw new, multiple exit paths, easy to forget delete on one of them
void riskyFunction(bool condition) {
    Resource* r = new Resource();

    if (condition) {
        return;      // LEAK — delete was never reached on this path!
    }

    r->use();
    delete r;        // only reached on the OTHER path
}

// SAFE — smart pointer guarantees cleanup on EVERY exit path
void safeFunction(bool condition) {
    unique_ptr<Resource> r = make_unique<Resource>();

    if (condition) {
        return;      // SAFE — r's destructor still runs during the return, cleanup guaranteed
    }

    r->use();
}   // SAFE — r's destructor runs here too, on the normal path

int main() {
    cout << "--- risky, early return ---" << endl;
    riskyFunction(true);    // "Resource released" NEVER prints — genuine leak

    cout << "--- safe, early return ---" << endl;
    safeFunction(true);       // "Resource released" DOES print — cleanup guaranteed either way

    return 0;
}
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Never let a raw `new` result exist unattended

```cpp
unique_ptr<Resource> r = make_unique<Resource>();
```

Wrap the allocation in a smart pointer in the exact same line it's created — there's no window where a bare, unmanaged pointer exists.

### Step 2 — Use the smart pointer exactly like a raw pointer for everything else

```cpp
r->use();
```

`->` and `*` work identically on smart pointers as on raw pointers — no change to how the object itself is actually used.

### Step 3 — Let every exit path clean up automatically

Whether the function returns early, throws, or reaches its natural end, the smart pointer's destructor runs and calls `delete` — no code needed at any of those exit points to make that happen.

---

## 🚀 Common Use Cases

- Replacing essentially all raw, manually-managed `new`/`delete` pairs in new code with `unique_ptr`
- Ensuring resource cleanup happens correctly even in functions with multiple early returns or exception-throwing code paths
- Passing ownership of a heap object into a container or another function via `std::move` on a `unique_ptr`, without ever manually tracking who's responsible for deleting it
- Auditing existing code for raw `new` calls as a checklist item — each one is a candidate for wrapping in a smart pointer instead

---

## ⚖️ Alternatives

### make_shared for genuinely shared ownership

```cpp
shared_ptr<Resource> r = make_shared<Resource>();
```

Use only when multiple independent parts of the program legitimately need the object to outlive any single one of them — `unique_ptr` should remain the default otherwise, per [[Smart Pointers - unique_ptr and shared_ptr]].

### Stack allocation instead of heap allocation, when possible

```cpp
void evenSafer() {
    Resource r;      // STACK allocated — no new, no delete, no smart pointer needed at all
    r.use();
}   // destructor runs automatically, exactly like any other local variable
```

The genuinely simplest fix: if the object doesn't actually need to outlive the function or be dynamically sized, skip heap allocation entirely and use a plain stack-allocated local variable.

---

## 🚨 Common Mistakes

- Allocating with raw `new` "just for now" intending to wrap it in a smart pointer later, and never actually getting back to it — the safest habit is wrapping it immediately, in the same statement.
- Extracting a raw pointer from a smart pointer via `.get()` and then manually `delete`-ing that raw pointer separately, causing a double-free the moment the smart pointer's own destructor also runs.
- Reaching for heap allocation and a smart pointer when a plain stack-allocated local variable would have worked just as well and required no dynamic memory management at all.
- Using `shared_ptr` reflexively instead of `unique_ptr`, adding unnecessary reference-counting overhead for what was really exclusive, single-owner usage.

---

## 💡 Wisdom from Mímir

Make it a hard rule: a raw `new` is only ever allowed to exist for the exact duration of the statement that immediately wraps it in a smart pointer. If you ever find yourself holding a bare `Type*` from `new` for more than that one line, that's the moment a leak becomes possible — close that window immediately, every time.

---

## 🔗 Related Notes

- [[Smart Pointers - unique_ptr and shared_ptr]]
- [[RAII - Resource Acquisition Is Initialization]]
- [[Memory Management - Stack, Heap, new, and delete]]
- [[C++ Codex]]
