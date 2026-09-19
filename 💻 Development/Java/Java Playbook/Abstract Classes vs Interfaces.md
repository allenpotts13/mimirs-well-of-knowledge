---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Java
difficulty: Intermediate
tags:
  - oop
  - abstract-classes
  - interfaces
aliases:
  - abstract keyword
  - Interface Default Methods
publish: true
permalink: java/abstract-classes-vs-interfaces
---

# <span class="rune">ᛟ</span> Abstract Classes vs Interfaces

> *An abstract class answers "what IS this thing" — it shares real identity and state through inheritance. An interface answers "what CAN this thing do" — it makes a promise about behavior, with no opinion at all about what the thing actually is.*

---

## 🎯 Purpose

Both abstract classes and interfaces let you define a contract that concrete classes must fulfill, without providing a complete implementation themselves — but they serve different design purposes and have different capabilities. Choosing between them (or, very often, using both together) is one of the more nuanced decisions in Java OOP design, and a frequent topic in coursework specifically because the distinction isn't always obvious at first.

---

## 🧠 Key Ideas

- An **abstract class** (`abstract class Shape`) can have both fully-implemented methods AND abstract (unimplemented) methods that subclasses must provide — and it can hold actual instance state (fields).
- An **interface** (`interface Drawable`) traditionally declared only method signatures with no implementation and no instance state — modern Java (8+) added **default methods** (with a body) and **static methods** to interfaces, blurring this line somewhat, but interfaces still cannot hold instance fields (only `public static final` constants).
- A class can `extend` only **one** abstract (or any) class, but can `implement` **multiple** interfaces — this is Java's answer to needing something like multiple inheritance.
- Abstract classes are chosen when subclasses share genuine common state and behavior (an "is-a" relationship with real shared implementation); interfaces are chosen to describe a capability or role that unrelated classes can all promise to fulfill (a "can-do" relationship).
- A class can't be instantiated directly from either an abstract class or an interface — only a concrete class that fully implements every abstract method (from either) can be instantiated with `new`.

---

## ⚙️ How It Works

An abstract class sits in the middle of an inheritance hierarchy — it can provide real, inherited implementation for common behavior while still leaving specific pieces (marked `abstract`) for subclasses to fill in, and every subclass inherits both the shared state and the shared code. An interface instead describes a pure capability, disconnected from any specific inheritance lineage — a `Duck` and an `Airplane` share no meaningful "is-a" relationship at all, but both can independently `implement Flyable`, because flying is a capability, not a shared identity.

```text
abstract class Shape {              // shares real state + some real behavior
    String color;                    // actual field — subclasses inherit it
    abstract double area();          // no implementation — subclasses MUST provide one
    void printColor() {               // real, shared implementation
        System.out.println("Color: " + color);
    }
}

interface Drawable {                 // describes a pure capability, no shared state
    void draw();                      // no implementation (unless declared default)
    default void printLabel() {        // Java 8+ default method — HAS an implementation
        System.out.println("Drawable object");
    }
}
```

---

## 💻 Examples

```java
abstract class Shape {
    protected String color;

    public Shape(String color) {
        this.color = color;
    }

    public abstract double area();     // MUST be implemented by any concrete subclass

    public void describe() {           // shared, concrete implementation — inherited as-is
        System.out.println(color + " shape with area " + area());
    }
}

class Circle extends Shape {
    private double radius;

    public Circle(String color, double radius) {
        super(color);
        this.radius = radius;
    }

    @Override
    public double area() {
        return Math.PI * radius * radius;
    }
}

interface Comparable2<T> {             // interface — a pure behavioral contract
    int compareTo(T other);
}

interface Serializable2 {
    default String serialize() {       // default method — DOES have a body
        return "generic serialization";
    }
}

// A class can implement MULTIPLE interfaces, but extend only ONE class
class Employee extends Shape implements Comparable2<Employee>, Serializable2 {
    // must provide compareTo(); area() inherited requirement already satisfied by Shape's subclass chain
    @Override
    public int compareTo(Employee other) { return 0; }
    @Override
    public double area() { return 0; } // just to satisfy the abstract requirement in this contrived example
}
```

---

## 🚀 Real World Applications

- Using an abstract class (`Shape`, `Employee`, `Vehicle`) to share real fields and common logic across a family of related, genuinely-related subclasses
- Using an interface (`Comparable`, `Runnable`, `Iterable`) to describe a capability that many unrelated classes across a codebase or library can independently promise to support
- Combining both: an abstract base class for shared implementation, plus one or more interfaces for additional cross-cutting capabilities the class also needs to support
- Java's own standard library conventions — `Comparable<T>` and `Iterable<T>` as interfaces (pure capability), `AbstractList`/`AbstractMap` as abstract classes (shared partial implementation for a family of related collection types)

---

## ⚖️ Advantages

- Abstract classes let genuinely related types share real code, avoiding duplication across similar subclasses.
- Interfaces allow a class to promise multiple unrelated capabilities simultaneously, something single-inheritance abstract classes can't do alone.
- Default methods on interfaces let library authors add new capability to an interface without breaking every class that already implements it.
- Together, they give Java a flexible answer to "share real implementation" (abstract classes) and "support multiple unrelated contracts" (interfaces) without full multiple inheritance's complexity.

---

## ⚠️ Limitations

- A class can extend only one abstract class, so overusing abstract classes for capability-sharing (rather than genuine is-a relationships) runs into Java's single-inheritance ceiling quickly.
- Default methods on interfaces blur what used to be a very clean distinction, and can cause a specific kind of ambiguity (a "diamond problem" variant) if a class implements two interfaces with conflicting default methods for the same method signature, requiring explicit resolution.
- Choosing between an abstract class and an interface is genuinely a design judgment call, not always a clear-cut rule, and gets debated frequently even among experienced developers.

---

## 🚨 Common Mistakes

- Using an abstract class purely to share a couple of default method implementations across otherwise-unrelated classes, when an interface (with default methods) would model the "shared capability, not shared identity" relationship more accurately.
- Forgetting a class implementing an interface must provide an implementation for every abstract (non-default) method, or the class itself must also be declared `abstract`.
- Trying to give an interface actual instance fields — interfaces can only hold `public static final` constants, never genuine per-instance state.
- Assuming interfaces can never have any implementation at all — this was true before Java 8, but default and static methods on interfaces have existed since then.

---

## 📖 Further Reading

- Oracle Java Tutorials: "Abstract Methods and Classes"
- Oracle Java Tutorials: "Interfaces" (including default methods)

---

## 💡 Wisdom from Mímir

When deciding between the two, ask the "is-a" vs "can-do" question directly: does this relationship genuinely share identity and state (abstract class), or does it just need to promise a capability that unrelated classes could also promise (interface)? Most real design confusion here comes from skipping that question and reaching for whichever one happens to be more familiar.

---

## 🔗 Related Notes

- [[Inheritance and the extends Keyword]]
- [[Polymorphism - Overriding vs Overloading]]
- [[Comparable vs Comparator]]
- [[Java Codex]]
