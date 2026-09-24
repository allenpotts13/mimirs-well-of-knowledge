---
type: concept
status: published
created: 2026-09-23
updated: 2026-09-23
technology: C++
difficulty: Advanced
tags:
  - cpp-fundamentals
  - rule-of-three
  - copy-semantics
aliases:
  - Copy Constructor
  - Copy Assignment Operator
  - Shallow Copy vs Deep Copy
publish: true
permalink: cpp/the-rule-of-three
---

# <span class="rune">ᛟ</span> The Rule of Three

> *Java never lets you copy an object by accident — assignment just copies the reference. C++ copies the actual object, member by member, by default, every time — and if one of those members is a raw pointer to heap memory, that default copy is quietly building a bug.*

---

## 🎯 Purpose

The **Rule of Three** states: if a class needs to define any one of a destructor, a copy constructor, or a copy assignment operator, it almost certainly needs to define all three. This concept doesn't really exist in Java or Python — both languages copy objects by reference by default (assigning one variable to another just makes two names for the same object), while C++ copies objects by *value* by default, member by member, unless told otherwise.

---

## 🧠 Key Ideas

- The **destructor** (`~ClassName()`) runs automatically when an object is destroyed — the natural place to release any resources (heap memory, file handles) the object owns.
- The **copy constructor** (`ClassName(const ClassName& other)`) defines what happens when a new object is created *as a copy* of an existing one (`ClassName b = a;`).
- The **copy assignment operator** (`ClassName& operator=(const ClassName& other)`) defines what happens when an *already-existing* object is assigned the value of another (`b = a;` where `b` already exists).
- If you write none of these three, the compiler generates default versions automatically — and the default copy behavior is a **shallow copy**: each member is copied field-by-field, which is fine for simple value members but disastrous for a raw pointer member, since both the original and the copy end up pointing at the *same* heap memory.
- A **deep copy** — writing your own version of these three functions to actually duplicate whatever a pointer member points to, rather than just copying the pointer's address — is what's needed whenever a class manually manages a resource like heap memory.

---

## ⚙️ How It Works

When a class contains a raw pointer to heap-allocated memory, the compiler's automatically-generated copy constructor and copy assignment operator do exactly what they'd do for any other member: copy the pointer's *value* — the address it holds — not what it points to. This means after `ClassName b = a;`, both `a` and `b` now hold a pointer to the *exact same* heap memory, not two independent copies. The danger surfaces the moment either object is destroyed: its destructor calls `delete` on that shared memory, and the *other* object is left holding a dangling pointer to memory that's already been freed — and if both destructors run (which they will, since both objects still exist), the same memory gets `delete`d twice, which is undefined behavior.

```text
class Bad {
    int* data;
public:
    Bad(int val) { data = new int(val); }
    ~Bad() { delete data; }     // no custom copy constructor/assignment written
};

Bad a(5);
Bad b = a;      // SHALLOW COPY — b.data and a.data point at the SAME memory now!
// when a and b are both destroyed, delete runs on that SAME address TWICE — undefined behavior
```

---

## 💻 Examples

```cpp
#include <iostream>
#include <cstring>
using namespace std;

// THE PROBLEM — relying on the compiler's default (shallow) copy behavior
class BadString {
    char* data;
public:
    BadString(const char* str) {
        data = new char[strlen(str) + 1];
        strcpy(data, str);
    }
    ~BadString() { delete[] data; }
    // No copy constructor or copy assignment defined — compiler generates SHALLOW ones
};

// void demonstrateBug() {
//     BadString a("hello");
//     BadString b = a;        // shallow copy — b.data and a.data point at the SAME memory
// }                              // both destructors run here — delete[] called TWICE on the same pointer — UB!

// THE FIX — following the Rule of Three, with a proper DEEP copy
class GoodString {
    char* data;
public:
    GoodString(const char* str) {
        data = new char[strlen(str) + 1];
        strcpy(data, str);
    }

    // 1. Destructor
    ~GoodString() {
        delete[] data;
    }

    // 2. Copy constructor — DEEP copy, allocates its OWN separate memory
    GoodString(const GoodString& other) {
        data = new char[strlen(other.data) + 1];
        strcpy(data, other.data);
    }

    // 3. Copy assignment operator — ALSO a deep copy, plus self-assignment and old-memory handling
    GoodString& operator=(const GoodString& other) {
        if (this == &other) return *this;   // guard against a = a (self-assignment)
        delete[] data;                         // release the OLD memory this object was holding
        data = new char[strlen(other.data) + 1];
        strcpy(data, other.data);
        return *this;
    }

    void print() { cout << data << endl; }
};

int main() {
    GoodString a("hello");
    GoodString b = a;         // uses the copy constructor — a genuinely SEPARATE copy now
    b.print();                   // "hello" — independent of a
    return 0;
}                                  // both destructors run safely — each has its OWN memory to free
```

---

## 🚀 Real World Applications

- Writing any class that manually manages a heap-allocated resource via raw pointers, and needing that class to be safely copyable
- Recognizing "double free" or "heap corruption" crashes as a strong signal that a class is relying on the compiler's default shallow copy when it actually needed a deep one
- Understanding why modern C++ code increasingly avoids this problem entirely by using [[Smart Pointers - unique_ptr and shared_ptr|smart pointers]] or container types instead of raw owning pointers, sidestepping the need to hand-write the Rule of Three at all
- Reading legacy or lower-level C++ code that manages its own memory and correctly evaluating whether its copy behavior is actually safe

---

## ⚖️ Advantages

- Understanding the Rule of Three deeply explains *why* C++'s default copy behavior can be dangerous — knowledge that transfers directly to reasoning about any manually-managed resource, not just memory.
- Writing a correct deep copy gives a class fully safe, predictable value semantics — copies behave exactly like independent objects, with no shared hidden state.
- This concept is foundational to understanding [[Move Semantics and Rvalue References|move semantics]], which exists specifically to make copying expensive resources faster when a genuine copy isn't actually needed.

---

## ⚠️ Limitations

- Hand-writing all three functions correctly (including the self-assignment guard, and correctly releasing old memory in the assignment operator) is genuinely easy to get subtly wrong.
- The Rule of Three only addresses copying — it doesn't cover moving (transferring ownership without copying at all), which modern C++ (C++11+) added as a related but distinct concern, sometimes extending this to the "Rule of Five" (adding a move constructor and move assignment operator).
- In modern C++, the *actual* best practice for most new code is to avoid raw owning pointers entirely (using smart pointers or standard containers instead), which sidesteps needing to hand-write the Rule of Three at all — but understanding it remains essential for reading and maintaining a huge amount of existing C++ code.

---

## 🚨 Common Mistakes

- Writing a class that manages a raw pointer to heap memory (a destructor that calls `delete`) without also writing a copy constructor and copy assignment operator, silently inheriting the compiler's dangerous default shallow-copy behavior.
- Forgetting the self-assignment guard (`if (this == &other) return *this;`) in a custom copy assignment operator — without it, `a = a;` can delete the very memory it's about to copy from, corrupting the object.
- Forgetting to release the *existing* memory in a custom copy assignment operator before allocating new memory for the copied data, causing a memory leak on every reassignment.
- Assuming this problem doesn't apply if a class "only has simple members" — the Rule of Three only becomes relevant the moment a class manages a resource like a raw pointer; classes with only value-type members are perfectly safe with the compiler's defaults.

---

## 📖 Further Reading

- cppreference.com: "Rule of three/five/zero"
- *Effective C++* (Scott Meyers) — Items on copy behavior and resource management

---

## 💡 Wisdom from Mímir

The moment a class needs a custom destructor at all, treat that as an alarm bell to immediately ask "does this class also need a copy constructor and copy assignment operator?" — nine times out of ten, if you're manually cleaning something up in a destructor, the compiler's default shallow copy is quietly setting up a double-free or dangling-pointer bug the very first time someone copies that object.

---

## 🔗 Related Notes

- [[Memory Management - Stack, Heap, new, and delete]]
- [[Move Semantics and Rvalue References]]
- [[Smart Pointers - unique_ptr and shared_ptr]]
- [[C++ Codex]]
