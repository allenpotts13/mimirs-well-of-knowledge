---
publish: true
status: published
permalink: cpp/cpp-cheat-sheet
---

# <span class="rune">ᛊ</span> C++ Cheat Sheet

> Dense reference across core C++, written for someone coming from Java/Python. Each section links back to the full [[C++ Codex|concept note]] for depth.

---

## Pointers & References

```cpp
int x = 10;
int* p = &x;          // p holds the ADDRESS of x
int& ref = x;           // ref is ANOTHER NAME for x — bound once, never reassignable

*p = 20;                  // dereference — modifies x THROUGH the pointer
ref = 30;                   // modifies x DIRECTLY — ref IS x

int* empty = nullptr;         // a pointer pointing at nothing
if (empty == nullptr) { }
```

`&` on the right of a declaration type = reference. `&` in an expression = address-of. `*` in a declaration = pointer type. `*` in an expression = dereference.

See: [[Pointers and References]]

---

## Memory: Stack vs Heap

```cpp
int stackVar = 10;              // STACK — automatic cleanup, scoped

int* heapVar = new int(20);       // HEAP — MANUAL cleanup required
delete heapVar;                     // must match every new
heapVar = nullptr;                    // good practice after delete

int* arr = new int[10];                // array allocation
delete[] arr;                            // MUST use delete[] for arrays, not delete
```

**Every `new` needs a matching `delete` (or `delete[]` for arrays).** No garbage collector — forgetting is a memory leak, not an exception.

See: [[Memory Management - Stack, Heap, new, and delete]] · [[Undefined Behavior]]

---

## Compiling

```bash
g++ file.cpp -o program           # compile + link in one step
./program                            # run it (Windows: program.exe)

g++ -c file.cpp -o file.o             # compile ONLY (no link)
g++ a.o b.o -o program                  # link separately

g++ -Wall -std=c++17 -g file.cpp -o program
#      ^warnings  ^C++ version  ^debug symbols
```

**Compiler error** = syntax/type problem. **Linker error** ("undefined reference") = something declared but never defined/linked in.

See: [[Compiling C++ - g++, clang, and the Build Toolchain]] · [[Reading Common Compiler Errors]]

---

## Headers & Multi-File Structure

```cpp
// MyClass.h
#ifndef MY_CLASS_H      // include guard — OR use: #pragma once
#define MY_CLASS_H
class MyClass {
public:
    MyClass(int x);        // DECLARATION only
    int getX() const;
private:
    int x;
};
#endif

// MyClass.cpp
#include "MyClass.h"
MyClass::MyClass(int x) : x(x) {}     // DEFINITION — note the :: scope resolution
int MyClass::getX() const { return x; }

// main.cpp
#include "MyClass.h"       // only needs the HEADER
```

```bash
g++ main.cpp MyClass.cpp -o program     # must compile/link BOTH files
```

`#include <...>` = standard library. `#include "..."` = your own project files.

See: [[The Compilation Model - Headers, Source Files, and Translation Units]] · [[Building a Multi-File Program]]

---

## Namespaces

```cpp
std::cout << "Hello";           // fully qualified — always works
using namespace std;              // brings ALL of std into scope — fine for small programs
using std::cout;                    // TARGETED import — just this one name

namespace MyMath {                    // defining your own namespace
    int add(int a, int b) { return a + b; }
}
MyMath::add(2, 3);
```

**Never** put `using namespace std;` in a header — it pollutes every file that includes it.

See: [[Namespaces]]

---

## Structs vs Classes

```cpp
struct Point { int x, y; };            // PUBLIC by default
class Account { double balance; public: /*...*/ };   // PRIVATE by default
```

**That's the only difference.** Both support constructors, methods, inheritance, everything.

See: [[Structs vs Classes in C++]]

---

## The Rule of Three

```cpp
class GoodString {
    char* data;
public:
    ~GoodString() { delete[] data; }                          // 1. Destructor

    GoodString(const GoodString& other) {                       // 2. Copy constructor — DEEP copy
        data = new char[strlen(other.data) + 1];
        strcpy(data, other.data);
    }

    GoodString& operator=(const GoodString& other) {              // 3. Copy assignment
        if (this == &other) return *this;                            // self-assignment guard
        delete[] data;
        data = new char[strlen(other.data) + 1];
        strcpy(data, other.data);
        return *this;
    }
};
```

**If a class needs a custom destructor, it almost certainly needs all three.** The compiler's default copy is shallow — copies the pointer, not what it points to.

See: [[The Rule of Three]]

---

## Move Semantics

```cpp
#include <utility>   // std::move

Buffer(Buffer&& other) noexcept : data(other.data) {   // MOVE constructor — steals, doesn't copy
    other.data = nullptr;                                 // source left safely empty
}

Buffer c = std::move(a);    // explicitly says "a is done, steal it" — a is now empty
```

`std::move` is just a cast — it doesn't move anything itself; it makes the object eligible for a move constructor/assignment to actually do the stealing.

See: [[Move Semantics and Rvalue References]]

---

## RAII & Smart Pointers

```cpp
#include <memory>

unique_ptr<Widget> w = make_unique<Widget>();   // EXCLUSIVE ownership, auto-deleted, cannot be copied
unique_ptr<Widget> b = std::move(a);              // transfer ownership — a is now empty

shared_ptr<Widget> x = make_shared<Widget>();       // SHARED ownership, reference-counted
shared_ptr<Widget> y = x;                             // ref count now 2 — deleted only when BOTH go out of scope
x.use_count();                                          // check current ref count
```

**Default to `unique_ptr`.** Reach for `shared_ptr` only when genuine shared ownership is needed. Never manually `delete` something a smart pointer already owns.

See: [[RAII - Resource Acquisition Is Initialization]] · [[Smart Pointers - unique_ptr and shared_ptr]] · [[Avoiding Memory Leaks with Smart Pointers]]

---

## Operator Overloading

```cpp
class Point {
public:
    int x, y;
    Point operator+(const Point& other) const {         // member — Point is on the LEFT
        return Point{x + other.x, y + other.y};
    }
    bool operator==(const Point& other) const {
        return x == other.x && y == other.y;
    }
};

ostream& operator<<(ostream& os, const Point& p) {        // FREE function — ostream is on the left, not Point
    os << "(" << p.x << ", " << p.y << ")";
    return os;                                               // return the stream to allow chaining
}
```

See: [[Operator Overloading]]

---

## Templates & the STL

```cpp
template <typename T>
T myMax(T a, T b) { return (a > b) ? a : b; }

myMax(3, 5);              // compiler generates a REAL int version
myMax(3.5, 2.1);             // AND a separate real double version — NOT type-erased like Java generics

template <typename T>
class Box { T value; public: Box(T v) : value(v) {} T get() const { return value; } };
```

```cpp
#include <vector>
#include <algorithm>

vector<int> nums = {5, 2, 8, 1};
sort(nums.begin(), nums.end());                       // ascending
sort(nums.begin(), nums.end(), greater<int>());         // descending
auto it = find(nums.begin(), nums.end(), 8);              // returns an ITERATOR, compare against .end()
if (it != nums.end()) { /* found */ }

for (int n : nums) { }             // read-only, copies each element
for (int& n : nums) { n *= 2; }      // MODIFIES in place — needs the reference
for (const auto& s : words) { }        // read-only, NO copy — best for large objects
```

`container.end()` is a boundary marker one-past-the-last-element — never dereference it directly.

See: [[Templates]] · [[The Standard Template Library Overview]] · [[Sorting and Searching with the STL]] · [[Range-Based For Loops and Iterators]]

---

## Function Parameters

```cpp
void byValue(int x) { x++; }              // COPY — caller's original unaffected
void byRef(int& x) { x++; }                 // caller's original IS modified
void byPointer(int* x) { (*x)++; }            // same effect as reference, but nullable/reassignable

void printName(const string& name) { }          // BEST for read-only access to large objects — no copy, no risk
```

**Rule of thumb:** small type + read-only → value. Large type + read-only → `const&`. Needs modification → `&`. Might be "nothing" → pointer.

See: [[Pass by Value vs Reference vs Pointer]]

---

## Returning Multiple Values

```cpp
#include <utility>   // pair
#include <tuple>       // tuple

pair<int, int> getMinMax(...) { return {lo, hi}; }
auto [lo, hi] = getMinMax(nums);                    // structured bindings (C++17+)

tuple<int, int, double> getStats(...) { return {sum, count, avg}; }
auto [sum, count, avg] = getStats(nums);
```

More than 2-3 related values → prefer a small named struct over pair/tuple for readability.

See: [[Returning Multiple Values]]

---

## Input & File I/O

```cpp
#include <limits>
int userNum;
while (!(cin >> userNum)) {                              // detect bad input
    cin.clear();                                             // reset the fail flag
    cin.ignore(numeric_limits<streamsize>::max(), '\n');        // discard the bad text still in the buffer
}
```

```cpp
#include <fstream>
ofstream outFile("data.txt");
outFile << "text" << endl;                    // no explicit close() needed — RAII handles it

ifstream inFile("data.txt");
string line;
while (getline(inFile, line)) { }               // read line by line
```

See: [[Reading Input Safely with cin]] · [[File IO with fstream]] · [[String Handling with std-string]]

---

## Debugging

```bash
g++ -g program.cpp -o program        # MUST compile with -g for meaningful debugger output
gdb ./program
(gdb) run
(gdb) backtrace                        # shows the EXACT line + call stack of a crash
```

**Common segfault causes:** nullptr dereference, dangling pointer (used after `delete`), out-of-bounds array/vector access, uninitialized pointer, stack overflow from unbounded recursion.

**A segfault not happening is not proof of correctness** — undefined behavior can silently "work" for a long time before it doesn't.

See: [[Debugging a Segmentation Fault]] · [[Undefined Behavior]]

---

## 🔗 Related Notes

- [[C++ Codex]]
- [[Java Cheat Sheet]]
- [[Python Cheat Sheet]]
