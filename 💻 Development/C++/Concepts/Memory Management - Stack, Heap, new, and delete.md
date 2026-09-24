---
type: concept
status: published
created: 2026-09-23
updated: 2026-09-23
technology: C++
difficulty: Intermediate
tags:
  - cpp-fundamentals
  - memory-management
  - heap
aliases:
  - new and delete
  - Memory Leaks C++
publish: true
permalink: cpp/memory-management-stack-heap-new-and-delete
---

# <span class="rune">ᛟ</span> Memory Management - Stack, Heap, new, and delete

> *Every `new` you write is a promise to write a matching `delete` somewhere. Java never asked you to make that promise. C++ does, and it never checks whether you kept it.*

---

## 🎯 Purpose

C++ has no garbage collector — memory allocated with `new` stays allocated until explicitly released with `delete`, forever, unless you do it yourself. This single fact is the biggest mental adjustment coming from Java or Python, both of which handle this automatically. Understanding the stack/heap split, and exactly when each is used, is the foundation everything else about C++ memory (including [[RAII - Resource Acquisition Is Initialization|RAII]] and [[Smart Pointers - unique_ptr and shared_ptr|smart pointers]], which exist specifically to make this safer) is built on top of.

---

## 🧠 Key Ideas

- The **stack** holds local variables and function call frames — allocation and cleanup are automatic and fast, but a stack variable's lifetime ends the instant it goes out of scope (the function returns, or the enclosing block ends).
- The **heap** holds memory that must outlive the function that created it, or whose size isn't known until runtime — allocated explicitly with `new`, and it stays allocated until explicitly released with `delete`.
- `new Type` allocates one object on the heap and returns a pointer to it; `new Type[n]` allocates an array of `n` objects and returns a pointer to the first one.
- `delete pointer` frees a single heap-allocated object; `delete[] pointer` frees a heap-allocated array — using the wrong form of `delete` for what was actually allocated is undefined behavior.
- A **memory leak** happens when heap memory is allocated with `new` but never released with `delete` — unlike Java (where the garbage collector eventually reclaims unreachable objects), that memory in C++ is gone for good until the entire program exits, no matter how unreachable it becomes.

---

## ⚙️ How It Works

When a function is called, its local variables are placed on the stack — a fast, automatically-managed region that shrinks the moment the function returns, cleanly destroying everything that was local to it. `new` instead reaches into a separate region, the heap, and carves out space that will remain allocated regardless of which function created it or whether that function has already returned — this is precisely why heap allocation is the right tool when data needs to outlive its creating function, or when the required size isn't known until the program is actually running. The tradeoff is that nothing automatically cleans the heap up — every `new` needs an explicit, matching `delete` somewhere, or that memory is never reclaimed for the life of the program.

```text
void makeOnStack() {
    int x = 5;          // STACK — automatically destroyed when makeOnStack() returns
}                          // x is GONE here, cleanly, no action needed

int* makeOnHeap() {
    int* p = new int(5);  // HEAP — survives past this function's return
    return p;                // the CALLER now owns this memory and must delete it eventually
}                              // p (the pointer variable) is gone, but what it POINTED TO is still allocated
```

---

## 💻 Examples

```cpp
#include <iostream>
using namespace std;

int main() {
    // Stack allocation — automatic, fast, scoped
    int stackVar = 10;
    // stackVar is automatically destroyed when main() ends — no action needed

    // Heap allocation — manual, must be explicitly freed
    int* heapVar = new int(20);
    cout << *heapVar << endl;    // 20
    delete heapVar;                // MUST do this manually, or it's a leak
    heapVar = nullptr;               // good practice — avoids an accidental "dangling pointer" reuse

    // Heap-allocated array — note the DIFFERENT delete form
    int* arr = new int[10];       // allocates 10 ints on the heap
    for (int i = 0; i < 10; i++) {
        arr[i] = i * i;
    }
    delete[] arr;                    // MUST use delete[], not delete, for an array
    arr = nullptr;

    // THE LEAK — forgetting to delete at all
    int* leaked = new int(99);
    // ... no delete anywhere ...
    // when leaked goes out of scope, the POINTER is gone, but the memory it pointed
    // to is now UNREACHABLE and UNRECOVERABLE for the rest of the program's life

    return 0;
}

// A common real pattern — heap allocation to survive past a function's return
// (must be its own top-level function — C++ doesn't allow nested function definitions)
int* createArray(int size) {
    int* result = new int[size];   // must outlive this function — heap is REQUIRED here
    return result;
}
```

---

## 🚀 Real World Applications

- Allocating an array whose size isn't known until runtime (a user-specified count, a file's actual contents)
- Creating an object that needs to outlive the function that constructs it, then get passed around and used elsewhere
- Building dynamic data structures (linked lists, trees, graphs) where nodes are created and destroyed throughout a program's execution, not tied to any one function's scope
- Understanding exactly why [[Smart Pointers - unique_ptr and shared_ptr|smart pointers]] exist — they wrap this entire `new`/`delete` discipline in a class that handles the `delete` automatically

---

## ⚖️ Advantages

- Manual memory management gives precise, predictable control over exactly when memory is allocated and released — no garbage collector pause, ever, which matters for performance-critical and real-time systems.
- The stack's automatic cleanup makes the common case (local variables with a clear, bounded lifetime) effortless and fast.
- Understanding this model deeply gives real insight into what Java and Python are doing for you automatically underneath their own memory management.

---

## ⚠️ Limitations

- Forgetting a `delete` causes a memory leak — invisible at first, but a real, serious problem in any long-running program, since leaked memory is never reclaimed.
- Using memory after `delete`-ing it (a "dangling pointer" access) is undefined behavior — it might work by coincidence, or it might crash, or corrupt unrelated data.
- Deleting the same pointer twice ("double-free") is also undefined behavior and a classic, hard-to-diagnose bug.
- This entire manual discipline is exactly what makes C++ memory bugs (leaks, dangling pointers, double-frees) a real, ongoing category of software vulnerabilities that simply don't exist in garbage-collected languages.

---

## 🚨 Common Mistakes

- Allocating with `new[]` (an array) but freeing with plain `delete` instead of `delete[]` — undefined behavior, since the two forms handle destructor calls and memory bookkeeping differently.
- Forgetting to `delete` heap memory at all, especially inside a function that allocates but never explicitly returns or frees it — a leak that accumulates every time that function runs.
- Deleting a pointer and then continuing to use it (a dangling pointer) instead of setting it to `nullptr` immediately afterward as a defensive habit.
- Returning a pointer to a stack-allocated local variable, confusing stack and heap lifetimes — the local variable is already destroyed by the time the caller tries to use the returned pointer.

---

## 📖 Further Reading

- cppreference.com: "new expression" and "delete expression"
- *Effective C++* (Scott Meyers) — Items on resource management

---

## 💡 Wisdom from Mímir

Every single `new` you write should have an obvious, traceable answer to "where does this get deleted?" before you move on to the next line. If you can't immediately answer that question, that's exactly the situation [[Smart Pointers - unique_ptr and shared_ptr|smart pointers]] exist to solve — modern C++ code reaches for raw `new`/`delete` far less often than you'd expect from an intro course.

---

## 🔗 Related Notes

- [[Pointers and References]]
- [[RAII - Resource Acquisition Is Initialization]]
- [[Smart Pointers - unique_ptr and shared_ptr]]
- [[C++ Codex]]
