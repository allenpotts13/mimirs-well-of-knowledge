---
type: cpp-pattern
status: published
created: 2026-09-23
updated: 2026-09-23
technology: C++
difficulty: Intermediate
tags:
  - segfault
  - debugging
  - undefined-behavior
aliases:
  - Segfault
  - Segmentation Fault Debugging
publish: true
permalink: cpp/debugging-a-segmentation-fault
---

# <span class="rune">ᚲ</span> Debugging a Segmentation Fault

> *A segfault is genuinely good news, in a strange way — it means the operating system caught you reaching into memory you don't own and stopped the program before it could do quieter, worse damage. The scary part isn't that it crashed; it's that it easily could have NOT crashed instead.*

---

## 🎯 Problem

A C++ program crashes immediately with "Segmentation fault (core dumped)" and no other information — no exception message, no stack trace by default, just a crash.

More generally, this pattern answers:
> What actually causes a segmentation fault, and how do I systematically track down which line triggered it?

---

## 🤔 Mental Model

A segmentation fault happens when a program tries to access memory the operating system never gave it permission to touch — like reaching for a book on a shelf that isn't actually inside your library at all. The OS notices the reach falls outside your library's boundary and stops you immediately, rather than letting you grab whatever happens to be sitting in that space (which could belong to something else entirely, and touching it could cause much subtler, harder-to-trace damage than an immediate crash).

---

## 🧠 Why This Pattern Works

Nearly every segfault traces back to one of a small handful of [[Undefined Behavior|undefined behavior]] patterns: dereferencing a `nullptr`, accessing an already-`delete`d ("dangling") pointer, reading or writing past the end of an array or `std::vector`, or dereferencing an uninitialized pointer that happens to hold a garbage address. Because a segfault gives no built-in message pointing at the actual line, a **debugger** (like `gdb`) is the standard tool for pinpointing exactly where the invalid access happened — running the program under the debugger and letting it catch the crash directly shows the offending line and the call stack that led there.

---

## 💻 C++ Solution — A Debugging Checklist

```cpp
#include <iostream>
using namespace std;

int main() {
    // Suspect #1 — dereferencing a nullptr
    int* p = nullptr;
    // cout << *p;                     // SEGFAULT — nothing valid at address 0

    // Suspect #2 — dangling pointer, used after delete
    int* q = new int(5);
    delete q;
    // cout << *q;                       // MIGHT segfault, might not — undefined behavior either way

    // Suspect #3 — out-of-bounds array/vector access
    int arr[5] = {1, 2, 3, 4, 5};
    // cout << arr[1000];                  // MIGHT segfault if far enough out of bounds

    // Suspect #4 — uninitialized pointer used before being assigned a real address
    int* r;
    // cout << *r;                           // r holds GARBAGE — could point anywhere, including invalid memory

    // Suspect #5 — stack overflow from unbounded recursion
    // int recurse() { return recurse(); }     // eventually exhausts the stack — often manifests as a segfault too

    return 0;
}
```

```bash
# Compile WITH debug symbols — required for gdb to show meaningful line numbers
g++ -g program.cpp -o program

# Run under gdb
gdb ./program
(gdb) run
# ... program crashes ...
(gdb) backtrace          # shows the EXACT line and call stack where the crash happened
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Compile with debug symbols

```bash
g++ -g program.cpp -o program
```

Without `-g`, a debugger can't map the crash back to meaningful source line numbers.

### Step 2 — Run under a debugger and let it catch the crash

```bash
gdb ./program
(gdb) run
```

The program runs normally until it segfaults — `gdb` then pauses execution right at the moment of the crash instead of just exiting.

### Step 3 — Get the exact location with a backtrace

```bash
(gdb) backtrace
```

Shows the specific line that crashed, plus the full chain of function calls that led there — this is the single most useful command for actually locating a segfault's cause.

### Step 4 — Cross-reference against the common suspects

Once you know the exact line, check it against the usual causes: a `nullptr` dereference, a dangling pointer, an out-of-bounds access, or an uninitialized pointer.

---

## 🚀 Common Use Cases

- Systematically tracking down the exact cause of a crash instead of guessing by reading code top to bottom
- Using a debugger's backtrace as the fastest path to the actual offending line, especially in a larger multi-file program
- Running a sanitizer (AddressSanitizer, `-fsanitize=address`) during development specifically to catch memory errors like this before they even reach a segfault
- Recognizing that "it crashed" is genuinely useful information — the alternative (silent memory corruption with no crash at all) is often far worse and harder to detect

---

## ⚖️ Alternatives

### Compiling with AddressSanitizer for more detailed diagnostics

```bash
g++ -fsanitize=address -g program.cpp -o program
./program
```

AddressSanitizer often produces a far more detailed report than a bare segfault — including exactly what kind of invalid access occurred (use-after-free, out-of-bounds, etc.) and where, without needing to manually drive `gdb`.

### Adding print statements as a lower-tech first pass

```cpp
cout << "Reached checkpoint 1" << endl;
// ... suspect code ...
cout << "Reached checkpoint 2" << endl;   // if this DOESN'T print, the crash is between the two checkpoints
```

A legitimate, simple technique for narrowing down roughly where a crash happens before reaching for a full debugger session.

---

## 🚨 Common Mistakes

- Trying to debug a segfault purely by reading the code without a debugger or print-statement narrowing, when the actual line is often genuinely not obvious from inspection alone.
- Forgetting to compile with `-g`, then being unable to get meaningful line numbers out of `gdb`'s backtrace.
- Assuming a segfault always happens exactly at the point of the bad memory access — sometimes memory corruption happens earlier and the crash only manifests later, when the corrupted memory is finally used in a way that trips the OS's protection.
- Assuming "it didn't segfault" means the code is correct — as covered in [[Undefined Behavior]], plenty of invalid memory access never triggers a segfault at all, and silently corrupts something else instead, which is arguably a worse outcome than a crash.

---

## 📖 Further Reading

- GDB documentation: "Getting Started" and the `backtrace` command
- AddressSanitizer documentation (part of the LLVM/Clang and GCC toolchains)

---

## 💡 Wisdom from Mímir

Don't treat a segfault as a mystery to solve by staring at the code — treat it as an invitation to run the program under a debugger and ask it directly where the crash happened. `gdb`'s `backtrace` command answers in seconds what could otherwise take an hour of guessing, and it's a skill that pays for itself the very first time a crash isn't in the line you assumed it would be.

---

## 🔗 Related Notes

- [[Undefined Behavior]]
- [[Pointers and References]]
- [[Memory Management - Stack, Heap, new, and delete]]
- [[C++ Codex]]
