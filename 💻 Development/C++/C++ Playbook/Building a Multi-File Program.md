---
type: cpp-pattern
status: published
created: 2026-09-23
updated: 2026-09-23
technology: C++
difficulty: Intermediate
tags:
  - project-structure
  - headers
  - compilation
aliases:
  - Multi-File C++ Project
  - Header and Source File Structure
publish: true
permalink: cpp/building-a-multi-file-program
---

# <span class="rune">ᚲ</span> Building a Multi-File Program

> *Splitting one working single-file program into a header, a source file, and a main file feels like pure ceremony the first time you do it — right up until the project has five classes and you realize this is the only reason it's still readable.*

---

## 🎯 Problem

A program that started as one simple `.cpp` file has grown large enough that it needs to be split into multiple files — a class's interface in a header, its implementation in a source file, and a separate `main.cpp` tying it all together.

More generally, this pattern answers:
> How do I structure a C++ project across multiple files, and what actually needs to go where?

---

## 🤔 Mental Model

This is the practical, hands-on version of everything covered conceptually in [[The Compilation Model - Headers, Source Files, and Translation Units]]: the header is the class's public "menu" — what it offers, without the recipe — and the `.cpp` file is the kitchen where the actual recipe (the implementation) lives. `main.cpp` is the dining room, ordering off the menu without ever needing to see how anything is actually prepared.

---

## 🧠 Why This Pattern Works

A class's declaration (its member variables, method signatures) goes in a `.h` file, wrapped in an include guard to prevent duplicate inclusion; the actual method bodies go in a matching `.cpp` file, which includes its own header to make sure the definitions match the declarations exactly. `main.cpp` (or any other file that needs to use the class) includes just the header — it never needs to see the implementation file at all, since the [[Compiling C++ - g++, clang, and the Build Toolchain|linker]] resolves the connection between the declaration it sees and the actual compiled implementation afterward.

---

## 💻 C++ Solution

```cpp
// ===== BankAccount.h =====
#ifndef BANK_ACCOUNT_H
#define BANK_ACCOUNT_H

class BankAccount {
private:
    double balance;

public:
    BankAccount(double initialBalance);   // just DECLARATIONS here
    void deposit(double amount);
    void withdraw(double amount);
    double getBalance() const;
};

#endif
```

```cpp
// ===== BankAccount.cpp =====
#include "BankAccount.h"
#include <stdexcept>

BankAccount::BankAccount(double initialBalance) : balance(initialBalance) {}

void BankAccount::deposit(double amount) {
    balance += amount;
}

void BankAccount::withdraw(double amount) {
    if (amount > balance) {
        throw std::runtime_error("Insufficient funds");
    }
    balance -= amount;
}

double BankAccount::getBalance() const {
    return balance;
}
```

```cpp
// ===== main.cpp =====
#include <iostream>
#include "BankAccount.h"      // only needs the HEADER — never sees BankAccount.cpp directly
using namespace std;

int main() {
    BankAccount account(100.0);
    account.deposit(50.0);
    cout << "Balance: " << account.getBalance() << endl;

    return 0;
}
```

```bash
# Compiling all three files together
g++ main.cpp BankAccount.cpp -o program
./program
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Declare the class interface in a header

```cpp
class BankAccount { ... };   // signatures only, no method bodies
```

Wrapped in an include guard (`#ifndef`/`#define`/`#endif`) so it's safe to include from multiple files without duplicate-definition errors.

### Step 2 — Implement the methods in a matching source file

```cpp
#include "BankAccount.h"
BankAccount::BankAccount(double initialBalance) : balance(initialBalance) {}
```

The `ClassName::methodName` syntax (called the "scope resolution operator") tells the compiler this definition belongs to the class declared in the header, not a free-standing function.

### Step 3 — Include only the header wherever the class is actually used

```cpp
#include "BankAccount.h"
```

`main.cpp` never needs `#include "BankAccount.cpp"` — the compiler compiles each `.cpp` file separately, and the [[Compiling C++ - g++, clang, and the Build Toolchain|linker]] connects the declaration `main.cpp` sees to the actual implementation compiled from `BankAccount.cpp`.

### Step 4 — Compile every .cpp file together into one executable

```bash
g++ main.cpp BankAccount.cpp -o program
```

Both files must be included in the same compile/link command — omitting `BankAccount.cpp` produces "undefined reference" linker errors for every method it was supposed to define.

---

## 🚀 Common Use Cases

- Splitting any class with real behavior into a header (interface) and source file (implementation) as a project grows beyond a single file
- Organizing a larger assignment or project into logically separate files, one class (or related group of functions) per header/source pair
- Reusing the same header across multiple `.cpp` files that all need to work with the same class
- Understanding a real-world C++ project's file layout, since virtually every non-trivial C++ codebase follows this exact structure

---

## ⚖️ Alternatives

### Header-only classes (no separate .cpp file)

```cpp
// SmallClass.h
class SmallClass {
public:
    int getValue() const { return 42; }   // defined INLINE, directly in the header
};
```

Sometimes used for very small classes, or for [[Templates|template classes]], which generally must be fully defined in the header anyway since templates need their full definition visible at every point of instantiation.

---

## 🚨 Common Mistakes

- Forgetting to include a newly-added `.cpp` file in the compile command, causing "undefined reference" linker errors for every method that file was supposed to provide.
- Putting actual method implementations directly in the header instead of a matching `.cpp` file, causing "multiple definition" linker errors the moment that header is included in more than one file that gets compiled and linked together.
- Forgetting an include guard on a new header, causing duplicate-definition errors the first time it's (even indirectly) included more than once in the same translation unit.
- Editing a class's implementation in the `.cpp` file but forgetting to update the matching declaration in the header (or vice versa), causing confusing signature-mismatch compiler errors.

---

## 💡 Wisdom from Mímir

The header/source split feels like unnecessary overhead the first time you do it for a single small class — the payoff shows up the moment a second file needs to use that same class, without needing to see (or accidentally duplicate) its actual implementation. Treat the split as the default the instant a class has any real behavior worth reusing, not something to defer until the project "really needs it."

---

## 🔗 Related Notes

- [[The Compilation Model - Headers, Source Files, and Translation Units]]
- [[Compiling C++ - g++, clang, and the Build Toolchain]]
- [[C++ Codex]]
