---
type: concept
status: published
created: 2026-09-23
updated: 2026-09-23
technology: C++
difficulty: Beginner
tags:
  - cpp-fundamentals
  - pointers
  - references
aliases:
  - Pointer vs Reference
  - "* and & in C++"
publish: true
permalink: cpp/pointers-and-references
---

# <span class="rune">ᛟ</span> Pointers and References

> *Java gives you a reference and hides everything about how it actually works. C++ gives you the same underlying idea — a variable that points at another variable's location — and hands you the raw mechanics to go with it.*

---

## 🎯 Purpose

A **pointer** is a variable that stores a memory address — the location where another value lives — rather than storing the value directly. A **reference** is an alias for an existing variable, a second name for the same memory. Both let code work with data indirectly instead of by copying it, but they behave differently enough that C++ treats them as two distinct tools. Neither concept exists explicitly in Java or Python — both languages use references internally but never expose the address or the `*`/`&` syntax to you.

---

## 🧠 Key Ideas

- `int* p;` declares a pointer to an `int` — `p` holds a memory address, not an integer value itself.
- `&variable` (the **address-of** operator) retrieves a variable's memory address — this is how a pointer gets pointed at something in the first place.
- `*p` (the **dereference** operator) accesses the value stored at the address `p` holds — "go to that address and get what's there."
- `int& ref = variable;` declares a **reference** — `ref` becomes another name for `variable` itself, not a separate variable holding an address. Unlike a pointer, a reference **must** be initialized immediately and can never be reassigned to refer to something else afterward.
- A pointer can be `nullptr` (pointing at nothing) and can be reassigned to point at different variables over its lifetime; a reference can do neither — it's bound once, permanently, at creation.

---

## ⚙️ How It Works

Every variable lives at some address in memory. `&variable` returns that address as a value, which can be stored in a pointer. Once a pointer holds an address, `*pointer` reads (or writes) whatever value currently lives there — the pointer itself is just a number (an address) with a type attached, telling the compiler how to interpret whatever's at that address. A reference skips the "store an address, then dereference it" two-step entirely — the compiler treats `ref` as directly, permanently synonymous with `variable`, resolved once at compile time rather than through an explicit runtime dereference.

```text
int x = 10;
int* p = &x;      // p now holds the ADDRESS of x
int& ref = x;      // ref is just ANOTHER NAME for x — not a separate thing at all

*p = 20;            // changes x, via the address p holds
ref = 30;             // ALSO changes x directly — ref IS x

cout << x;             // 30
```

---

## 💻 Examples

```cpp
#include <iostream>
using namespace std;

int main() {
    int x = 10;

    // A pointer — holds an address
    int* p = &x;
    cout << "Value of x: " << x << endl;
    cout << "Address of x: " << p << endl;
    cout << "Value AT that address: " << *p << endl;

    *p = 20;                    // modifies x THROUGH the pointer
    cout << "x is now: " << x << endl;   // 20

    // A reference — another name for x
    int& ref = x;
    ref = 30;                    // modifies x DIRECTLY — ref IS x
    cout << "x is now: " << x << endl;   // 30

    // Pointers can be reassigned; references cannot
    int y = 100;
    p = &y;                       // p now points at y instead — perfectly legal
    // ref = y;                    // this does NOT rebind ref — it copies y's VALUE into x!

    // nullptr — a pointer pointing at nothing
    int* empty = nullptr;
    if (empty == nullptr) {
        cout << "empty points at nothing" << endl;
    }

    return 0;
}
```

---

## 🚀 Real World Applications

- Passing large objects to functions "by reference" (or by pointer) to avoid an expensive copy — the core reason [[Pass by Value vs Reference vs Pointer]] exists as its own topic
- Building linked data structures (linked lists, trees) where each node needs to reference other nodes
- Working with dynamically allocated memory (see [[Memory Management - Stack, Heap, new, and delete]]), which is only ever accessed through a pointer
- Implementing optional "no value" semantics with `nullptr`, since a reference can never represent "nothing" the way a pointer can

---

## ⚖️ Advantages

- Pointers provide maximum flexibility — reassignable, nullable, and usable in dynamic data structures.
- References give the safety of "always refers to something valid" combined with clean, no-`*`-needed syntax at every use site.
- Together they let C++ pass and manipulate data without unnecessary copying, which matters enormously for performance-sensitive code.

---

## ⚠️ Limitations

- A pointer can be `nullptr`, uninitialized (pointing at garbage), or "dangling" (pointing at memory that's already been freed) — none of which the compiler catches for you, unlike Java's `NullPointerException` at least giving you a clear runtime signal.
- References cannot be reassigned or left uninitialized, which is safer but also less flexible — they can't represent "no value" the way a pointer's `nullptr` can.
- Dereferencing a bad pointer (`nullptr`, dangling, or uninitialized) is **undefined behavior** (see [[Undefined Behavior]]) — it might crash immediately, corrupt memory silently, or appear to work fine until much later.

---

## 🚨 Common Mistakes

- Confusing `&` used for address-of (`&x`, in an expression) with `&` used to declare a reference type (`int& ref`, in a declaration) — same symbol, two completely different meanings depending on context.
- Assuming `ref = y;` rebinds a reference to point at `y` instead — it doesn't; it copies `y`'s *value* into whatever `ref` already refers to, since a reference can never be re-seated after initialization.
- Dereferencing a pointer without checking whether it's `nullptr` first, causing a crash (or worse, silent corruption) at runtime.
- Returning a pointer or reference to a local variable from a function — the local variable is destroyed the moment the function returns, leaving a dangling reference to memory that's no longer valid.

---

## 📖 Further Reading

- cppreference.com: "Pointer declaration" and "Reference declaration"
- *A Tour of C++* (Bjarne Stroustrup) — Chapter 1, on pointers, arrays, and references

---

## 💡 Wisdom from Mímir

Whenever `*`/`&` syntax feels confusing, read it as a question of direction: `&x` asks "where does x live?" (giving you an address), while `*p` asks "what's living at this address?" (giving you back a value). Every pointer/reference confusion untangles once you're clear on which of those two questions a given line is actually asking.

---

## 🔗 Related Notes

- [[Memory Management - Stack, Heap, new, and delete]]
- [[Pass by Value vs Reference vs Pointer]]
- [[Smart Pointers - unique_ptr and shared_ptr]]
- [[C++ Codex]]
