---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Java
difficulty: Beginner
tags:
  - oop
  - enums
  - type-safety
aliases:
  - enum keyword
  - Enum with Methods
publish: true
permalink: java/enums-in-java
---

# <span class="rune">ᛟ</span> Enums in Java

> *A Java enum isn't a glorified list of integer constants the way it is in some languages — it's a real class, and every one of its values is a genuine, full-fledged object that can carry its own fields, constructor, and methods.*

---

## 🎯 Purpose

`enum` defines a fixed, type-safe set of named constants — days of the week, card suits, order statuses — anything with a known, closed set of possible values. Unlike using plain `int` or `String` constants (a common pattern in older code or other languages), Java enums are checked by the compiler, can't hold an invalid value, and are genuinely full classes capable of having their own fields, constructors, and methods per constant.

---

## 🧠 Key Ideas

- `enum Day { MONDAY, TUESDAY, WEDNESDAY, ... }` defines a fixed set of exactly these values — no other `Day` value can ever exist.
- Every enum implicitly extends `java.lang.Enum` and automatically gets useful methods for free: `.name()`, `.ordinal()` (its position, zero-indexed, in declaration order), `.values()` (a static method returning all constants as an array), and `.valueOf(String)` (parsing a `String` back into the matching constant).
- Enums can have fields, a constructor, and methods — each constant can carry its own associated data, set once via the constructor when the enum type is first loaded.
- Enums work naturally and safely with `switch` — the compiler can verify every case is (or isn't) handled, and switch expressions (see [[Control Flow - if, switch, and Loops]]) can enforce full exhaustiveness over an enum's known set of values.
- Enum constants are compared safely with `==` — since there's exactly one single instance of each constant in the entire JVM, reference equality is both correct and idiomatic for enums (unlike the general `==` pitfalls with other reference types).

---

## ⚙️ How It Works

Each enum constant is actually a `public static final` instance of the enum type itself, created exactly once when the enum class is first loaded — `MONDAY` isn't a placeholder for an integer, it's a genuine singleton object of type `Day`. This is precisely why enums can carry fields and behave like ordinary objects: a constructor runs once per constant at class-loading time, and any fields declared execute that logic and store per-constant data permanently.

```text
enum Planet {
    MERCURY(3.303e+23, 2.4397e6),
    VENUS(4.869e+24, 6.0518e6);

    private final double mass;   // per-constant field
    private final double radius;

    Planet(double mass, double radius) {  // runs ONCE per constant, at class load time
        this.mass = mass;
        this.radius = radius;
    }

    double surfaceGravity() {
        return 6.67300E-11 * mass / (radius * radius);
    }
}
```

---

## 💻 Examples

```java
public enum OrderStatus {
    PENDING, SHIPPED, DELIVERED, CANCELLED;
}

OrderStatus status = OrderStatus.SHIPPED;
System.out.println(status.name());     // "SHIPPED"
System.out.println(status.ordinal());  // 1 (zero-indexed position in declaration)

for (OrderStatus s : OrderStatus.values()) {
    System.out.println(s);
}

OrderStatus parsed = OrderStatus.valueOf("DELIVERED"); // parses a String into the matching constant

// Exhaustive switch expression over an enum
String description = switch (status) {
    case PENDING -> "Order received";
    case SHIPPED -> "On its way";
    case DELIVERED -> "Completed";
    case CANCELLED -> "Order cancelled";
};

// Enum with fields, a constructor, and per-constant behavior
public enum Planet {
    MERCURY(3.303e+23, 2.4397e6),
    EARTH(5.976e+24, 6.37814e6);

    private final double mass;
    private final double radius;

    Planet(double mass, double radius) {
        this.mass = mass;
        this.radius = radius;
    }

    public double surfaceGravity() {
        return 6.67300E-11 * mass / (radius * radius);
    }
}

System.out.println(Planet.EARTH.surfaceGravity());

// Enums compare safely with ==, since each constant is a single unique instance
OrderStatus a = OrderStatus.SHIPPED;
OrderStatus b = OrderStatus.SHIPPED;
System.out.println(a == b); // true — always safe for enum constants
```

---

## 🚀 Real World Applications

- Representing a fixed set of states (order status, connection state, HTTP methods, days of the week) with full compiler-checked type safety
- Attaching per-constant data and behavior (like `Planet`'s mass/radius/gravity above) instead of maintaining a separate lookup table alongside plain constants
- Writing exhaustive `switch` expressions over an enum's values, with the compiler flagging any missing case
- Replacing error-prone "magic string" or "magic number" constants (`"PENDING"`, `1`) with a genuinely type-safe alternative the compiler can validate

---

## ⚖️ Advantages

- Compile-time type safety completely eliminates an entire category of "invalid value" bugs that plain `int`/`String` constants are prone to.
- Being genuine classes, enums can carry real behavior and per-constant data, going well beyond what a simple named-constant list could offer.
- Safe, idiomatic `==` comparison and clean, exhaustive `switch` support make enums pleasant and low-risk to work with throughout a codebase.

---

## ⚠️ Limitations

- Enums can't be extended further — an enum type is implicitly `final`, and you can't create a subclass of a specific enum the way you could with an ordinary class.
- `.ordinal()` reflects declaration order, which is fragile — reordering or inserting a new constant changes every subsequent constant's ordinal value, breaking any code that persisted or relied on that specific numeric value.
- Adding fields/behavior to every constant (as in the `Planet` example) can make a very large enum feel closer to a small class hierarchy, and at that point a genuine class hierarchy might communicate intent more clearly.

---

## 🚨 Common Mistakes

- Persisting or serializing an enum's `.ordinal()` value directly (e.g., storing it in a database) — reordering the enum's declared constants later silently changes what that stored number now means, corrupting previously-saved data.
- Using `.valueOf()` on a string that doesn't exactly match any constant name (including case) and not handling the resulting `IllegalArgumentException`.
- Treating enum constants as if they were merely fancy integers, missing the opportunity to attach real per-constant fields and behavior when the design calls for it.
- Forgetting that adding a new constant to an existing enum, in production code depending on exhaustive `switch` expressions elsewhere, will trigger a compile error at every such switch until the new case is explicitly handled — which is a feature, not a bug, but can be surprising the first time it happens.

---

## 📖 Further Reading

- Oracle Java Tutorials: "Enum Types"
- Oracle documentation: `java.lang.Enum` class reference

---

## 💡 Wisdom from Mímir

Never persist an enum's `.ordinal()` value anywhere that outlives the running program — a database column, a file format, a network protocol. Store its `.name()` (the string) instead. Ordinal values shift the moment someone reorders or inserts a constant, silently corrupting anything that depended on the old numbering.

---

## 🔗 Related Notes

- [[Control Flow - if, switch, and Loops]]
- [[Classes and Objects]]
- [[Java Codex]]
