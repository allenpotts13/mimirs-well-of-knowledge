---
type: concept
status: published
created: 2026-09-23
updated: 2026-09-23
technology: C++
difficulty: Advanced
tags:
  - cpp-fundamentals
  - move-semantics
  - rvalue-references
aliases:
  - "&& in C++"
  - std::move
publish: true
permalink: cpp/move-semantics-and-rvalue-references
---

# <span class="rune">ᛟ</span> Move Semantics and Rvalue References

> *Copying a 500-megabyte buffer just to hand it off to a function that only needed it temporarily is real, wasted work. Move semantics exist because C++ noticed it was doing that everywhere, and gave you a way to say "just take it" instead of "make a copy."*

---

## 🎯 Purpose

Before C++11, passing or returning an object that owned an expensive resource (like [[The Rule of Three|a class managing heap memory]]) meant a full, expensive copy every time, even when the original was about to be discarded anyway. **Move semantics** let a resource's ownership be *transferred* from a temporary or soon-to-be-discarded object to a new one, cheaply, instead of copied. This concept has no real equivalent in Java or Python — both languages already pass objects by reference, so there's never a "copy vs. move" decision to make in the first place.

---

## 🧠 Key Ideas

- An **lvalue** is anything with a persistent identity/name you could take the address of (`int x; x` is an lvalue); an **rvalue** is a temporary value with no lasting identity (the literal `5`, or the result of an expression like `a + b`).
- `Type&&` (double ampersand) declares an **rvalue reference** — a reference that specifically binds to rvalues (temporaries), distinct from the ordinary `Type&` reference, which binds to lvalues.
- A **move constructor** and **move assignment operator** take an rvalue reference parameter and *steal* the source object's internal resources (pointers, buffers) directly, leaving the source in a valid-but-empty state, rather than duplicating everything the way a copy would.
- `std::move(x)` doesn't actually move anything by itself — it's a cast that tells the compiler "treat `x` as an rvalue," making it eligible for move operations even though `x` is technically an lvalue (a named variable) — the actual "moving" happens inside whatever move constructor/assignment is then invoked.
- Move operations are, by design, meant to be cheap — typically just copying a few pointers and resetting the source — dramatically faster than a deep copy of the underlying resource.

---

## ⚙️ How It Works

When an object is about to be discarded anyway (a temporary returned from a function, or explicitly marked with `std::move`), there's no real reason to deep-copy its internal resources into a new object — the source was going away regardless. A move constructor exploits this: instead of allocating new memory and copying data (like [[The Rule of Three|a proper copy constructor]] would), it simply takes the source's existing pointer, assigns it directly to the new object, and sets the source's pointer to `nullptr` — transferring ownership rather than duplicating the underlying data, at a fraction of the cost.

```text
Copy:  new_obj.data = new Type[...]; copy every element from old_obj.data to new_obj.data;
         (old_obj is UNCHANGED and still owns its own separate memory)

Move:  new_obj.data = old_obj.data;   old_obj.data = nullptr;
         (new_obj now owns what USED to belong to old_obj — old_obj is left safely empty)
```

---

## 💻 Examples

```cpp
#include <iostream>
#include <utility>   // for std::move
using namespace std;

class Buffer {
    int* data;
    size_t size;
public:
    Buffer(size_t n) : size(n) {
        data = new int[n];
        cout << "Constructed, allocated " << n << " ints" << endl;
    }

    // Copy constructor — expensive, duplicates everything
    Buffer(const Buffer& other) : size(other.size) {
        data = new int[size];
        for (size_t i = 0; i < size; i++) data[i] = other.data[i];
        cout << "Copied (expensive!)" << endl;
    }

    // Move constructor — cheap, STEALS the pointer instead of copying
    Buffer(Buffer&& other) noexcept : data(other.data), size(other.size) {
        other.data = nullptr;    // leave the SOURCE in a valid, empty state
        other.size = 0;
        cout << "Moved (cheap!)" << endl;
    }

    ~Buffer() {
        delete[] data;    // safe even if data is nullptr — delete on nullptr is a no-op
    }
};

Buffer createBuffer() {
    Buffer temp(1000);
    return temp;    // the compiler can MOVE temp out, instead of copying it, since temp is about to be destroyed anyway
}

int main() {
    Buffer a(100);
    Buffer b = a;                   // COPY constructor — a is an lvalue, still needed afterward
    Buffer c = std::move(a);          // MOVE constructor — explicitly telling the compiler "a is done, steal it"
    Buffer d = createBuffer();          // MOVE constructor — temp was a temporary anyway (or elided entirely)

    // After std::move(a), "a" is in a valid-but-EMPTY state — using it further is legal but meaningless
    return 0;
}
```

---

## 🚀 Real World Applications

- Returning large objects (containers, buffers) from functions efficiently — modern compilers often move (or even fully elide) the copy automatically
- Using `std::move` explicitly when you know a variable is done being used and want to transfer its resource ownership cheaply into another object
- Understanding why standard library containers (`std::vector`, `std::string`) became dramatically faster to pass around and return from functions starting with C++11
- Writing move constructors/assignment for your own resource-owning classes, alongside the [[The Rule of Three|copy versions]], as part of what's sometimes called the "Rule of Five"

---

## ⚖️ Advantages

- Move semantics eliminate a huge amount of previously-unavoidable, wasted copying, especially for functions that construct and return large objects.
- `std::move` gives explicit, deliberate control over when a transfer-of-ownership is intended, rather than relying purely on compiler inference.
- Modern C++ code that embraces move semantics can approach the performance of manual, hand-optimized resource management while keeping high-level, readable syntax.

---

## ⚠️ Limitations

- After `std::move(x)`, `x` is left in a "valid but unspecified" state — it's still safe to destroy or reassign, but its actual contents shouldn't be relied upon, which is an easy rule to forget.
- Writing a correct move constructor/assignment adds real complexity to a class, on top of the [[The Rule of Three|Rule of Three]] it typically extends.
- `std::move` is just a cast — it doesn't actually perform any moving itself, and calling it on an object whose class has no move constructor defined silently falls back to an ordinary (expensive) copy instead, with no error or warning.

---

## 🚨 Common Mistakes

- Using a variable's value normally after calling `std::move(variable)` on it, not realizing its internal state has likely been stolen and is no longer meaningful.
- Assuming `std::move` itself does the actual work of moving — it only casts the expression to an rvalue reference; the real transfer happens in whatever move constructor or move assignment operator actually gets invoked as a result.
- Forgetting to mark a move constructor `noexcept` — some standard library operations (like `std::vector` resizing) specifically check for this and fall back to copying instead of moving if a move constructor might throw.
- Writing a move constructor that fails to leave the source object in a genuinely valid (even if empty) state — the source's destructor will still run eventually, and it needs to handle that emptied state safely (like `delete`-ing a `nullptr`, which is always safe).

---

## 📖 Further Reading

- cppreference.com: "Move constructors" and "std::move"
- *Effective Modern C++* (Scott Meyers) — the chapters specifically covering rvalue references and move semantics

---

## 💡 Wisdom from Mímir

Think of `std::move(x)` as saying "I'm done with `x` — you can have its insides" rather than "copy `x` for me." The moment you use a variable normally *after* moving from it, you've broken that promise — the variable itself is still alive and safe to touch, but whatever it used to contain is no longer guaranteed to be there.

---

## 🔗 Related Notes

- [[The Rule of Three]]
- [[Memory Management - Stack, Heap, new, and delete]]
- [[Smart Pointers - unique_ptr and shared_ptr]]
- [[C++ Codex]]
