---
type: concept
status: published
created: 2026-09-23
updated: 2026-09-23
technology: C++
difficulty: Beginner
tags:
  - cpp-fundamentals
  - structs
  - classes
aliases:
  - struct vs class
  - Default Access Specifier
publish: true
permalink: cpp/structs-vs-classes-in-cpp
---

# <span class="rune">ᛟ</span> Structs vs Classes in C++

> *Java's `class` and C++'s `struct` look like they shouldn't even be related — but in C++, a struct can have constructors, methods, inheritance, everything a class can. The only actual difference is one word's worth of default privacy.*

---

## 🎯 Purpose

Java has no `struct` keyword at all — everything is a class. Python doesn't have one natively either (though it has dataclasses, and simple attribute containers). C++ has both `struct` and `class`, and coming from either language, the natural assumption is that a C++ `struct` is a stripped-down, data-only version of a class — but in C++, that's not actually true. The two are nearly identical; the only real difference is a single default.

---

## 🧠 Key Ideas

- In C++, `struct` and `class` support **exactly the same features** — constructors, destructors, methods, inheritance, access specifiers (`public`/`private`/`protected`), everything.
- The **only** actual difference: a `struct`'s members are `public` by default; a `class`'s members are `private` by default. Explicitly writing `public:`/`private:` overrides either default identically.
- This is a legacy of C++'s history — `struct` originally came from C (where it really was just a plain data container with no methods at all), and C++ extended it to have full class capabilities while keeping the old keyword's default-public behavior for backward compatibility.
- Convention, not the compiler, is what actually separates their typical use: `struct` is idiomatically used for simple, mostly-public data-holding types (a `Point` with just `x`/`y`); `class` is idiomatically used when encapsulation (private data with controlled access) is the actual design goal.
- Inheritance defaults also differ the same way: `struct Derived : Base` inherits `public` by default; `class Derived : Base` inherits `private` by default — matching each type's own general default-access philosophy.

---

## ⚙️ How It Works

The compiler treats `struct` and `class` identically in every respect except for filling in the *default* access specifier when none is written explicitly — `struct` assumes `public:` until told otherwise, `class` assumes `private:` until told otherwise. Because both support the full feature set (methods, constructors, private members, inheritance), the choice between them in modern C++ is purely a readability signal to other developers about the type's intended role, not a genuine capability difference the way it might appear coming from a language where only one of the two concepts exists at all.

```text
struct Point {
    int x, y;                  // PUBLIC by default — no access specifier needed
};

class Account {
    double balance;              // PRIVATE by default — no access specifier needed
public:
    Account(double b) : balance(b) {}
};
```

---

## 💻 Examples

```cpp
#include <iostream>
using namespace std;

// struct — public by default, idiomatically used for simple data
struct Point {
    int x;
    int y;
};

// class — private by default, idiomatically used when encapsulation matters
class BankAccount {
    double balance;         // PRIVATE — no access specifier written, but this is the class default

public:
    BankAccount(double initialBalance) : balance(initialBalance) {}

    double getBalance() { return balance; }
    void deposit(double amount) { balance += amount; }
};

int main() {
    Point p;
    p.x = 3;              // works directly — Point's members are PUBLIC by default
    p.y = 4;
    cout << p.x << ", " << p.y << endl;

    BankAccount acc(100);
    // acc.balance = 999;      // COMPILE ERROR — balance is PRIVATE by default in a class
    acc.deposit(50);              // must go through the public method instead
    cout << acc.getBalance() << endl;

    return 0;
}

// Proving they're otherwise identical — a struct CAN have private members and methods too
struct Circle {
private:               // struct's default is public, but you CAN override it explicitly
    double radius;
public:
    Circle(double r) : radius(r) {}
    double area() { return 3.14159 * radius * radius; }
};
```

---

## 🚀 Real World Applications

- Using `struct` for simple, largely-public data aggregates — coordinates, RGB colors, small records passed around as a group
- Using `class` for anything where encapsulation genuinely matters — hiding internal state behind a controlled public interface, matching the same [[Encapsulation]]-style discipline from Java
- Reading unfamiliar C++ code and correctly interpreting a `struct` declaration as fully capable of having methods and private members, not assuming it's automatically a "dumb" data type
- Choosing `struct` vs `class` in your own code as a readability signal to future readers about the type's intended role, not a technical constraint

---

## ⚖️ Advantages

- Having both keywords lets developers signal design intent (data aggregate vs. encapsulated object) directly through the choice of keyword, even though the compiler treats them almost identically.
- `struct`'s public-by-default behavior avoids unnecessary `public:` boilerplate for genuinely simple data-holding types.
- Full feature parity means there's never a technical wall forcing a `struct` to be converted to a `class` later if it needs to grow more sophisticated behavior.

---

## ⚠️ Limitations

- The near-total feature overlap can be genuinely confusing coming from languages where `struct` (if it exists at all) is a fundamentally different, more limited concept.
- Because the difference is "just" a default, it's easy to accidentally leave a `struct`'s sensitive data public without realizing an explicit `private:` was needed — the language won't warn you, since public-by-default is `struct`'s intended behavior.
- Relying purely on convention (rather than a hard rule) for when to use which keyword means different codebases and different developers can reasonably disagree about the "correct" choice in any given case.

---

## 🚨 Common Mistakes

- Assuming a C++ `struct` cannot have constructors, methods, or private members — it can have all of them; the only actual difference from `class` is the default access level.
- Leaving genuinely sensitive data public in a `struct` by relying on its default, when a `class` (or an explicit `private:` inside the struct) was actually the more appropriate choice.
- Assuming `struct` behaves like a C `struct` (plain data only, no methods at all) — C++ struct is a full C++ type, not the more limited C version, even though the name and default-public behavior are inherited from C's legacy.
- Forgetting that inheritance defaults also differ (`struct` inherits `public` by default, `class` inherits `private` by default) and being surprised when a derived type's inherited members aren't accessible as expected.

---

## 📖 Further Reading

- cppreference.com: "Classes" (covers `struct`/`class` equivalence explicitly)
- *A Tour of C++* (Bjarne Stroustrup) — the chapter introducing classes and structs together

---

## 💡 Wisdom from Mímir

Treat the `struct`/`class` choice the same way you'd treat choosing a variable name — it's a signal to the next reader about intent, not a technical constraint the compiler enforces beyond the one default it fills in. If you find yourself needing private members and careful encapsulation inside something you declared as a `struct`, that's usually a sign the design has outgrown the "simple data aggregate" role the keyword was signaling in the first place.

---

## 🔗 Related Notes

- [[The Rule of Three]]
- [[Operator Overloading]]
- [[C++ Codex]]
