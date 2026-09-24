---
type: concept
status: published
created: 2026-09-23
updated: 2026-09-23
technology: C++
difficulty: Intermediate
tags:
  - cpp-fundamentals
  - undefined-behavior
  - safety
aliases:
  - UB
  - Undefined Behavior in C++
publish: true
permalink: cpp/undefined-behavior
---

# <span class="rune">ᛟ</span> Undefined Behavior

> *In Java, an out-of-bounds array access throws a clear, catchable exception, every single time. In C++, the exact same mistake might crash, might silently corrupt unrelated data, or might just... work — and which one happens can change between compilers, optimization settings, or even different runs of the same program.*

---

## 🎯 Purpose

**Undefined behavior (UB)** is C++'s term for actions the language standard deliberately places no requirements on — the compiler is free to do *anything* in response, including something that looks like it works. This is fundamentally different from Java's approach, where nearly every risky operation (null dereference, bad array index, bad cast) throws a specific, well-defined exception. Understanding UB — and taking it seriously — is essential, because it's the reason C++ bugs can be so much harder to track down than equivalent mistakes in a managed language.

---

## 🧠 Key Ideas

- Undefined behavior means the C++ standard makes **zero guarantees** about what happens — not "it throws an error," not "it does something specific but unsafe" — genuinely anything, including appearing to work perfectly.
- Common sources of UB: dereferencing a `nullptr` or dangling pointer, reading/writing past the end of an array, using a variable before it's initialized, signed integer overflow, and calling `delete` twice on the same pointer.
- UB is different from a **compiler error** (caught before the program even runs) and different from a **runtime exception** (caught while running, with a clear cause) — it's a category where nothing catches it at all, by design.
- The genuinely dangerous part: UB-triggering code frequently "works" during testing (especially in a simple debug build) and only breaks later — in a different environment, a different compiler, a different optimization level, or under a different, seemingly unrelated input.
- Tools exist specifically to catch UB that the compiler and runtime otherwise miss — sanitizers (like AddressSanitizer, UBSan) and tools like Valgrind detect many common UB patterns during testing, well before they'd surface as a mysterious production bug.

---

## ⚙️ How It Works

The C++ standard defines the language's behavior only for a specific, bounded set of well-formed operations — anything outside that boundary (like reading array index `10` on an array of size `5`) is explicitly left unspecified, on the theory that requiring the compiler to check every such case would cost real performance that not every program needs to pay for. In practice, this means the compiler is allowed to assume UB never happens, and can generate code (especially at higher optimization levels) that behaves in ways that look actively bizarre if that assumption turns out to be wrong — not because the compiler is "buggy," but because it was never obligated to handle that case sensibly in the first place.

```text
int arr[5] = {1, 2, 3, 4, 5};
cout << arr[10];        // UB — could print garbage, could crash, could "work" and print something plausible-looking
                            // the compiler made NO promise about what happens here, in either direction
```

---

## 💻 Examples

```cpp
#include <iostream>
using namespace std;

int main() {
    // Out-of-bounds array access — UB, no bounds checking in raw arrays
    int arr[5] = {1, 2, 3, 4, 5};
    cout << arr[10] << endl;    // UB — might print garbage, might crash, might "work"

    // Dereferencing a nullptr — UB
    int* p = nullptr;
    // cout << *p << endl;         // UB — typically crashes (a segfault), but not GUARANTEED to

    // Using an uninitialized variable — UB
    int x;                          // NOT initialized to 0 the way Java would
    // cout << x << endl;             // UB — could print anything, including a leftover value from earlier memory use

    // Dangling pointer — using memory after it's been freed
    int* heapVar = new int(5);
    delete heapVar;
    // cout << *heapVar << endl;      // UB — the memory MIGHT still hold 5 by coincidence, or might not

    // Double delete — freeing the same memory twice
    int* another = new int(10);
    delete another;
    // delete another;                 // UB — could crash immediately, could corrupt the heap silently

    // Signed integer overflow — UB (unlike unsigned overflow, which IS well-defined to wrap)
    int maxInt = 2147483647;
    // int overflowed = maxInt + 1;      // UB — NOT guaranteed to wrap to a negative number, even though it usually does

    return 0;
}
```

---

## 🚀 Real World Applications

- Recognizing that a C++ program "working during testing" is not proof of correctness — UB can hide indefinitely until a specific compiler, platform, or optimization level exposes it
- Running a debug build with sanitizers enabled (AddressSanitizer, UndefinedBehaviorSanitizer) specifically to catch UB patterns before they ship
- Understanding why a seemingly small, unrelated code change ("I only touched the logging") can suddenly make a pre-existing latent bug visible — the compiler's optimization decisions can shift in ways that expose UB that was always there
- Being appropriately skeptical of any C++ code that "looks fine" but does something the language explicitly leaves undefined — array bounds, pointer validity, initialization order

---

## ⚖️ Advantages

- Leaving certain operations undefined (rather than mandating expensive runtime checks for all of them) is part of what makes C++ capable of extremely high performance — you only pay for safety checks you explicitly add yourself.
- Modern tooling (sanitizers, static analyzers) has gotten genuinely good at catching a large share of common UB patterns during development, well before release.
- Understanding UB deeply builds a much sharper mental model of what's actually happening at the machine level — insight that's simply invisible in a managed language like Java.

---

## ⚠️ Limitations

- UB is, by definition, not something the compiler or runtime will warn you about — there's no exception to catch, no error message pointing at the actual cause.
- The same UB-triggering code can behave completely differently across compilers, optimization levels, or platforms, making bugs caused by it notoriously hard to reproduce and debug.
- Relying on "it worked when I tested it" as proof of correctness is fundamentally unsound for any code that might be triggering UB, since UB has no obligation to manifest consistently.

---

## 🚨 Common Mistakes

- Testing a program once, seeing it "work," and concluding a piece of code involving arrays/pointers is definitely correct — UB can remain silent through many runs and then manifest under a different compiler or input.
- Assuming signed integer overflow wraps around predictably (like it would in Java) — it's UB in C++, not a defined wraparound, even though it often *appears* to wrap on most real hardware.
- Assuming an uninitialized variable defaults to `0` the way Java's fields do — C++ locals are **not** automatically zero-initialized, and reading one before assigning it is UB.
- Treating "the program didn't crash" as equivalent to "the program is correct" — UB's most dangerous property is that it very often doesn't crash at all.

---

## 📖 Further Reading

- cppreference.com: "Undefined behavior" (with a categorized list of common UB triggers)
- *What Every C Programmer Should Know About Undefined Behavior* — LLVM Project blog series (the underlying concept transfers directly to C++)

---

## 💡 Wisdom from Mímir

Coming from Java, retrain the instinct that "it ran without an error" means "it's correct." In C++, a program riddled with undefined behavior can run flawlessly a hundred times and then fail the hundred-and-first, on a machine you don't control, for a reason that traces back to a mistake made long before the symptom ever appeared. Treat every array index, every pointer dereference, and every uninitialized variable as a place worth double-checking — the language genuinely will not catch it for you.

---

## 🔗 Related Notes

- [[Pointers and References]]
- [[Memory Management - Stack, Heap, new, and delete]]
- [[Debugging a Segmentation Fault]]
- [[C++ Codex]]
