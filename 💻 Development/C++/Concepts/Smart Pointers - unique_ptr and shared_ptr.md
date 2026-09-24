---
type: concept
status: published
created: 2026-09-23
updated: 2026-09-23
technology: C++
difficulty: Intermediate
tags:
  - cpp-fundamentals
  - smart-pointers
  - unique-ptr
aliases:
  - unique_ptr
  - shared_ptr
  - Smart Pointers C++
publish: true
permalink: cpp/smart-pointers-unique-ptr-and-shared-ptr
---

# <span class="rune">ᛟ</span> Smart Pointers - unique_ptr and shared_ptr

> *A smart pointer isn't a different kind of pointer — it's an ordinary object, following [[RAII - Resource Acquisition Is Initialization|RAII]], that happens to hold a raw pointer internally and calls `delete` on it automatically in its own destructor. All the memory-safety benefit, none of the manual bookkeeping.*

---

## 🎯 Purpose

Smart pointers are the modern C++ answer to manual `new`/`delete`: wrapper classes that manage a heap-allocated object's lifetime automatically, calling `delete` for you the instant the smart pointer itself goes out of scope. This is C++'s way of recovering much of the safety Java's garbage collector provides automatically, without giving up manual control entirely — and in modern C++ code, raw owning pointers (plain `Type*` used to actually own heap memory) are now considered poor style specifically because smart pointers solve the same problem far more safely.

---

## 🧠 Key Ideas

- `std::unique_ptr<Type>` owns a heap object **exclusively** — it cannot be copied (only moved, see [[Move Semantics and Rvalue References]]), guaranteeing exactly one owner at a time and therefore no ambiguity about who's responsible for deleting it.
- `std::shared_ptr<Type>` allows **multiple** owners of the same heap object, using an internal reference count — the object is only actually deleted once the *last* `shared_ptr` pointing at it is destroyed.
- Both are RAII wrappers (see [[RAII - Resource Acquisition Is Initialization]]) — their destructors automatically call `delete` on the managed object, meaning you never write `delete` yourself when using them correctly.
- `std::make_unique<Type>(args...)` and `std::make_shared<Type>(args...)` are the preferred way to *create* a smart pointer — safer and more efficient than constructing one from a raw `new` expression directly.
- A raw pointer obtained from a smart pointer via `.get()` should never be manually `delete`d — that pointer is still owned and managed by the smart pointer, and deleting it separately causes a double-free.

---

## ⚙️ How It Works

`unique_ptr` wraps a raw pointer and, following the [[RAII - Resource Acquisition Is Initialization|RAII pattern]], calls `delete` on it inside its own destructor — since a `unique_ptr` can't be copied (its copy constructor is explicitly deleted), there's never a moment where two `unique_ptr`s could both think they own — and both try to delete — the same memory. `shared_ptr` relaxes this by keeping an internal, shared reference count alongside the pointer: every time a `shared_ptr` is copied, the count increments; every time one is destroyed, the count decrements; only when that count reaches zero does the actual `delete` happen, guaranteeing the object outlives every `shared_ptr` that's still pointing at it.

```text
unique_ptr<Widget> a = make_unique<Widget>();
unique_ptr<Widget> b = a;              // COMPILE ERROR — unique_ptr cannot be copied
unique_ptr<Widget> b = std::move(a);     // fine — OWNERSHIP transfers to b, a is now empty

shared_ptr<Widget> x = make_shared<Widget>();   // ref count = 1
shared_ptr<Widget> y = x;                          // ref count = 2 — BOTH now legitimately share ownership
// object is only actually deleted once BOTH x and y go out of scope (ref count reaches 0)
```

---

## 💻 Examples

```cpp
#include <iostream>
#include <memory>
using namespace std;

class Widget {
public:
    Widget() { cout << "Widget created" << endl; }
    ~Widget() { cout << "Widget destroyed" << endl; }
    void greet() { cout << "Hello from Widget" << endl; }
};

int main() {
    // unique_ptr — exclusive ownership, automatic cleanup
    {
        unique_ptr<Widget> w = make_unique<Widget>();
        w->greet();                                        // -> works just like a raw pointer
    }   // Widget's destructor runs AUTOMATICALLY here — no delete needed anywhere

    // unique_ptr cannot be copied — only moved
    unique_ptr<Widget> a = make_unique<Widget>();
    // unique_ptr<Widget> b = a;              // COMPILE ERROR
    unique_ptr<Widget> b = std::move(a);          // ownership TRANSFERS to b; a is now empty (nullptr)

    // shared_ptr — multiple owners, reference-counted
    {
        shared_ptr<Widget> x = make_shared<Widget>();
        cout << "Ref count: " << x.use_count() << endl;   // 1
        {
            shared_ptr<Widget> y = x;                          // SHARED ownership — both point at the same object
            cout << "Ref count: " << x.use_count() << endl;      // 2
        }   // y goes out of scope — ref count drops back to 1, object NOT yet destroyed
        cout << "Ref count: " << x.use_count() << endl;             // 1
    }   // x goes out of scope — ref count drops to 0 — Widget's destructor NOW runs

    // What NOT to do — mixing raw and smart pointers dangerously
    Widget* raw = new Widget();
    unique_ptr<Widget> managed(raw);
    // delete raw;    // DON'T — managed's destructor will ALSO try to delete this same pointer — double free

    return 0;
}
```

---

## 🚀 Real World Applications

- Replacing raw `new`/`delete` almost entirely in modern C++ code — a `unique_ptr` is the default choice whenever a single object clearly owns a heap resource
- Using `shared_ptr` specifically when genuine shared ownership is needed — multiple parts of a program legitimately need the object to stay alive as long as any of them still need it
- Passing a `unique_ptr` by `std::move` into a function to transfer ownership explicitly and unambiguously
- Recognizing that virtually all modern C++ style guides and code reviews expect smart pointers over raw owning pointers, precisely because they eliminate the [[Memory Management - Stack, Heap, new, and delete|manual leak/double-free risk]] almost entirely

---

## ⚖️ Advantages

- Automatic, guaranteed cleanup eliminates the most common category of C++ memory bugs (leaks, double-frees) almost entirely, without sacrificing manual control over allocation timing the way a garbage collector would.
- `unique_ptr`'s move-only semantics make ownership explicit and unambiguous in the type system itself — you can tell at a glance that only one thing owns this resource at a time.
- `shared_ptr` cleanly solves the genuinely harder problem of shared ownership without requiring manual reference counting to be hand-written.

---

## ⚠️ Limitations

- `shared_ptr`'s reference counting has real runtime overhead (atomic increment/decrement) compared to `unique_ptr`'s essentially free ownership transfer — `unique_ptr` should be the default, with `shared_ptr` reserved for cases that genuinely need shared ownership.
- Circular `shared_ptr` references (object A holds a `shared_ptr` to B, B holds one back to A) prevent the reference count from ever reaching zero, causing a memory leak despite using smart pointers correctly — `std::weak_ptr` exists specifically to break this cycle.
- Mixing raw pointers and smart pointers carelessly (extracting a raw pointer via `.get()` and manually `delete`-ing it) reintroduces exactly the double-free risk smart pointers exist to prevent.

---

## 🚨 Common Mistakes

- Manually calling `delete` on a pointer that's already managed by a smart pointer, causing a double-free the moment the smart pointer's own destructor also runs.
- Trying to copy a `unique_ptr` directly instead of using `std::move` to transfer ownership — this is a compile error, which is actually the language protecting you from an ambiguous-ownership situation.
- Creating a `shared_ptr` cycle (two objects holding `shared_ptr`s to each other) and being surprised neither is ever destroyed, since each keeps the other's reference count above zero indefinitely.
- Using `shared_ptr` by default "to be safe," when `unique_ptr` would have been sufficient and more efficient — genuine shared ownership is less common in practice than it might initially seem.

---

## 📖 Further Reading

- cppreference.com: `std::unique_ptr`, `std::shared_ptr`, `std::weak_ptr`
- *Effective Modern C++* (Scott Meyers) — the chapter on smart pointers

---

## 💡 Wisdom from Mímir

Default to `unique_ptr` for essentially everything, and only reach for `shared_ptr` once you can clearly articulate *why* more than one thing genuinely needs to co-own this specific object. "I might need to share it later" isn't that reason — `unique_ptr` can always be converted to `shared_ptr` later if the need for actual shared ownership genuinely materializes.

---

## 🔗 Related Notes

- [[RAII - Resource Acquisition Is Initialization]]
- [[Memory Management - Stack, Heap, new, and delete]]
- [[Move Semantics and Rvalue References]]
- [[Avoiding Memory Leaks with Smart Pointers]]
- [[C++ Codex]]
