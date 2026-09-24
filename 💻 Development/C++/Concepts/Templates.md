---
type: concept
status: published
created: 2026-09-23
updated: 2026-09-23
technology: C++
difficulty: Advanced
tags:
  - cpp-fundamentals
  - templates
  - generic-programming
aliases:
  - Template Functions
  - Template Classes
  - Generics vs Templates
publish: true
permalink: cpp/templates
---

# <span class="rune">ᛟ</span> Templates

> *Java's generics are a compile-time-only illusion — erased down to Object by the time your program actually runs. C++ templates are the opposite: the compiler generates a genuinely separate, fully-typed version of your code for every distinct type you actually use it with.*

---

## 🎯 Purpose

**Templates** let a function or class be written once and work with any type, with the compiler generating a fully type-specific version for each distinct type actually used — C++'s answer to the same problem Java's generics and Python's duck typing both solve, but through a fundamentally different mechanism: genuine compile-time code generation rather than type erasure or runtime flexibility.

---

## 🧠 Key Ideas

- `template <typename T>` (or the older, equivalent `template <class T>`) introduces a placeholder type `T`, usable throughout the following function or class definition.
- Unlike Java's generics — which use [[Generics and Type Parameters|type erasure]], compiling down to a single `Object`-based implementation regardless of the actual type argument — C++ templates are **instantiated** separately for each distinct type actually used, producing genuinely different compiled code for `Template<int>` versus `Template<string>`.
- A template isn't compiled into real code at all until it's actually used with a specific type — this is why template-related compiler errors often only appear at the point of *use*, not at the point where the template itself was originally written.
- **Function templates** (`template <typename T> T max(T a, T b)`) and **class templates** (`template <typename T> class Container { ... }`) both follow this same instantiation model.
- The C++ standard library's own containers (`std::vector<T>`, `std::map<K, V>`) are themselves class templates — every `std::vector<int>` and `std::vector<string>` you use is a separately-instantiated, fully type-specific piece of generated code.

---

## ⚙️ How It Works

When the compiler encounters `max<int>(3, 5)` (or infers `int` automatically from the arguments), it generates an entire, genuinely separate compiled function specifically for `int` — as if you'd hand-written `int max(int a, int b) { ... }` yourself. Using the same template with `string` later causes the compiler to generate a *second*, entirely separate version for `string`. This is fundamentally different from Java's generics, where `List<Integer>` and `List<String>` compile down to the *same* underlying bytecode operating on `Object` — C++'s approach costs more compiled code size (one copy per type actually used) in exchange for zero runtime overhead and the ability to use type-specific operations (like `+` on numbers) directly inside the template body.

```text
template <typename T>
T max(T a, T b) { return (a > b) ? a : b; }

max(3, 5);          // compiler generates a REAL int max(int, int) — instantiated HERE
max(3.5, 2.1);         // compiler generates a SEPARATE, real double max(double, double)
max("abc", "xyz");       // a THIRD separate instantiation, this time for whatever string type is inferred
```

---

## 💻 Examples

```cpp
#include <iostream>
using namespace std;

// A function template
template <typename T>
T myMax(T a, T b) {
    return (a > b) ? a : b;
}

// A class template
template <typename T>
class Box {
    T value;
public:
    Box(T v) : value(v) {}
    T get() const { return value; }
    void set(T v) { value = v; }
};

int main() {
    // Function template — the compiler infers T from the arguments
    cout << myMax(3, 5) << endl;           // T inferred as int
    cout << myMax(3.5, 2.1) << endl;         // T inferred as double
    cout << myMax<string>("abc", "xyz") << endl;   // T EXPLICITLY specified as string

    // Class template — T specified explicitly when creating an instance
    Box<int> intBox(42);
    Box<string> stringBox("hello");

    cout << intBox.get() << endl;       // 42
    cout << stringBox.get() << endl;      // "hello"

    // The standard library's containers are templates too — this is the SAME mechanism
    vector<int> numbers = {1, 2, 3};        // vector<int> is a full, separately-instantiated type
    vector<string> words = {"a", "b"};        // vector<string> is a DIFFERENT, separately-instantiated type

    return 0;
}

// Multiple template parameters
template <typename K, typename V>
class Pair {
    K key;
    V value;
public:
    Pair(K k, V v) : key(k), value(v) {}
    K getKey() const { return key; }
    V getValue() const { return value; }
};

Pair<string, int> p("age", 30);
```

---

## 🚀 Real World Applications

- Every use of a standard library container (`std::vector`, `std::map`, `std::set`) — all class templates, instantiated for whatever specific type you use them with
- Writing a single generic algorithm (a sort, a search, a comparison function) that works identically across many unrelated types without duplicating code
- Building your own reusable, type-safe container or utility class that needs to work with more than one specific type
- Recognizing template-related compiler errors as pointing to the specific point of *instantiation* (where the template was actually used with a given type), not necessarily the template's original definition

---

## ⚖️ Advantages

- Zero runtime overhead — because each instantiation is a fully separate, concretely-typed piece of compiled code, there's no boxing, no runtime type checks, no performance cost compared to hand-writing a type-specific version yourself.
- Templates can use operators and behaviors specific to the actual type (like arithmetic operators) directly in the template body, something Java's type-erased generics fundamentally cannot do without extra workarounds.
- The standard library's extensive use of templates (containers, algorithms) provides an enormous amount of reusable, type-safe, high-performance functionality out of the box.

---

## ⚠️ Limitations

- Template-related compiler errors are notoriously dense and difficult to read, especially with heavily nested templates — a genuinely well-known pain point even among experienced C++ developers.
- Each distinct instantiation is separately compiled code, which can meaningfully increase compiled binary size ("code bloat") if a template is used with many different types.
- Templates must generally be fully defined in header files (not split into a separate `.cpp` implementation file the normal way), since the compiler needs the full definition available at every point of instantiation — a genuine departure from the usual [[The Compilation Model - Headers, Source Files, and Translation Units|header/source split]].

---

## 🚨 Common Mistakes

- Assuming C++ templates behave like Java generics (type-erased, one shared implementation) — they don't; each type actually used gets its own separately compiled version, with real performance and code-size implications.
- Splitting a template's declaration and definition across a header and a `.cpp` file the normal way, then hitting confusing linker errors — templates generally need their full definition visible in the header itself.
- Trying to use a template with a type that doesn't support an operation the template body requires (like `>` for `myMax`), and being confronted with a dense, hard-to-parse compiler error pointing at the point of instantiation rather than a clear, simple message.
- Assuming a template function/class is compiled once, generically — it's compiled fresh, separately, for every genuinely distinct type it's actually instantiated with.

---

## 📖 Further Reading

- cppreference.com: "Templates"
- *A Tour of C++* (Bjarne Stroustrup) — the chapter on templates and generic programming

---

## 💡 Wisdom from Mímir

When a template-related compiler error looks overwhelming, look for the *specific type* being used at the actual point of instantiation, not the template's original definition — the real problem is almost always "this specific type doesn't support what the template body needs it to do," and the mountain of surrounding template-machinery text in the error is mostly noise around that one core fact.

---

## 🔗 Related Notes

- [[The Standard Template Library Overview]]
- [[The Compilation Model - Headers, Source Files, and Translation Units]]
- [[C++ Codex]]
