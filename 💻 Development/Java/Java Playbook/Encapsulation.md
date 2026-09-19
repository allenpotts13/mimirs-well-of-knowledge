---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Java
difficulty: Beginner
tags:
  - oop
  - encapsulation
  - getters-setters
aliases:
  - Information Hiding
  - Getters and Setters
publish: true
permalink: java/encapsulation
---

# <span class="rune">ᛟ</span> Encapsulation

> *A private field with a public getter isn't bureaucracy for its own sake — it's the one seam in the class where you get to say "yes, but only if" before a value is ever allowed to change.*

---

## 🎯 Purpose

Encapsulation is the practice of hiding an object's internal data (making fields `private`) and exposing controlled access through public methods (getters and setters) — the first of Java's core OOP pillars. It's not about ceremony; it's about giving a class the power to enforce its own rules (validation, computed values, invariants) at the one place data actually changes, instead of trusting every piece of calling code to behave correctly on its own.

---

## 🧠 Key Ideas

- Fields are made `private` (see [[Access Modifiers]]) so they can't be modified directly from outside the class.
- A **getter** (`getFieldName()`) provides read access; a **setter** (`setFieldName(value)`) provides write access — and critically, a setter can validate or transform the incoming value before actually storing it.
- Encapsulation lets a class change its *internal* representation later without breaking any code that uses its public getters/setters — the internal implementation is free to evolve as long as the public contract stays the same.
- Not every field needs both a getter and a setter — a read-only property just omits the setter; a computed property's "getter" can calculate a value on the fly rather than returning a stored field at all.
- Immutable classes (see also [[String Immutability and the String Pool]]) take encapsulation further by providing no setters at all — every field is set once, in the constructor, and never changed afterward.

---

## ⚙️ How It Works

Without encapsulation, a `public` field can be set to *any* value from anywhere in the program — including invalid ones, like a negative age or an empty required name — with no way for the class to object. A `private` field accessed only through a setter gives the class exactly one place to intercept every attempted change and reject or adjust it before it's actually stored, which is impossible to guarantee once a field is directly `public` and writable from arbitrary outside code.

```text
public int age;              // ANYONE can do: obj.age = -5;  — no way to stop it

private int age;
public void setAge(int age) {
    if (age < 0) {
        throw new IllegalArgumentException("Age cannot be negative");
    }
    this.age = age;           // only reachable if the value passed validation
}
```

---

## 💻 Examples

```java
public class BankAccount {
    private double balance;   // hidden — no direct outside access

    public BankAccount(double initialBalance) {
        if (initialBalance < 0) {
            throw new IllegalArgumentException("Initial balance cannot be negative");
        }
        this.balance = initialBalance;
    }

    // Getter — read access
    public double getBalance() {
        return balance;
    }

    // Controlled "setter" — enforces a business rule instead of a raw setter
    public void deposit(double amount) {
        if (amount <= 0) {
            throw new IllegalArgumentException("Deposit amount must be positive");
        }
        balance += amount;
    }

    public void withdraw(double amount) {
        if (amount > balance) {
            throw new IllegalStateException("Insufficient funds");
        }
        balance -= amount;
    }
}

BankAccount acc = new BankAccount(100);
acc.deposit(50);              // goes through validation
// acc.balance = 1000000;     // COMPILE ERROR — balance is private, no direct access
System.out.println(acc.getBalance()); // 150
```

---

## 🚀 Real World Applications

- Enforcing business rules (non-negative balances, required fields, valid ranges) at the exact point data is set, rather than trusting every caller to validate independently
- Exposing computed, read-only properties (like `getFullName()` returning `firstName + " " + lastName`) without storing redundant data
- Changing a class's internal storage mechanism (switching from a field to a calculated value, or vice versa) without breaking any external code using its public getters
- Designing immutable value objects where every field is set once in the constructor and never exposed for modification afterward

---

## ⚖️ Advantages

- Centralizes validation and business rules in one place, rather than duplicating checks across every piece of code that might modify an object's state.
- Allows internal implementation details to change freely over time without breaking external code, as long as the public method signatures remain stable.
- Makes invalid object states much harder to accidentally create, since every mutation path can be controlled and checked.

---

## ⚠️ Limitations

- Writing a getter and setter for every single field without any actual validation or logic adds boilerplate without providing any real benefit over a plain public field — encapsulation's value comes from what happens *inside* those methods, not merely their existence.
- Overly granular getters/setters for every internal detail can still leak implementation specifics if not designed thoughtfully, partially defeating the purpose.
- Excessive validation logic scattered across many small setters can sometimes be harder to review holistically than a single well-documented constructor or validation method.

---

## 🚨 Common Mistakes

- Writing a plain getter and setter pair for every field with zero actual logic inside them ("bean-style" boilerplate), missing the actual point of encapsulation — which is the ability to *enforce rules*, not just to have methods instead of fields.
- Returning a direct reference to a mutable internal field from a getter (e.g., returning an internal `List` directly), which lets outside code bypass encapsulation entirely by mutating the returned object — a defensive copy or unmodifiable wrapper is often needed instead.
- Making fields `public` "temporarily" for convenience during development and never circling back to properly encapsulate them.
- Validating in the constructor but forgetting to apply the same validation in a setter that can change the same field later, leaving an inconsistent enforcement path.

---

## 📖 Further Reading

- Oracle Java Tutorials: "Controlling Access to Members of a Class"
- Oracle Java Tutorials: "Bean Properties" (getter/setter naming conventions)

---

## 💡 Wisdom from Mímir

Before writing a setter, ask what should happen if someone tries to set an invalid value — if the honest answer is "nothing, it just gets stored," that's a sign a plain field might genuinely be enough. Encapsulation earns its keep exactly at the moment a setter has an opinion about what values are acceptable.

---

## 🔗 Related Notes

- [[Access Modifiers]]
- [[Classes and Objects]]
- [[Inheritance and the extends Keyword]]
- [[Java Codex]]
