---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Java
difficulty: Beginner
tags:
  - oop
  - inheritance
  - extends
aliases:
  - Java Inheritance
  - Superclass and Subclass
publish: true
permalink: java/inheritance-and-the-extends-keyword
---

# <span class="rune">ᛟ</span> Inheritance and the extends Keyword

> *Every class you write in Java already has a parent, whether you write `extends` or not — `Object` is standing there implicitly, one level up, waiting.*

---

## 🎯 Purpose

Inheritance lets one class (the **subclass**) acquire the fields and methods of another (the **superclass**) via `extends`, modeling "is-a" relationships — a `Dog` *is an* `Animal`. This is the mechanism that enables code reuse across related types and forms the foundation [[Polymorphism - Overriding vs Overloading|polymorphism]] is built on top of.

---

## 🧠 Key Ideas

- `class Dog extends Animal` makes `Dog` a subclass of `Animal`, automatically inheriting all of `Animal`'s non-private fields and methods.
- Java supports only **single inheritance** for classes — a class can `extend` exactly one other class, never multiple (unlike some other languages) — though a class can implement multiple interfaces, covered in [[Abstract Classes vs Interfaces]].
- If a class doesn't explicitly `extend` anything, it implicitly extends `java.lang.Object` — every class in Java has `Object` somewhere in its inheritance chain.
- A subclass's constructor must call a superclass constructor (via `super(...)`, or implicitly if the superclass has a no-arg constructor) as its very first action, ensuring the inherited portion of the object is properly initialized before the subclass's own fields are set up.
- Private fields/methods of a superclass are NOT directly accessible in a subclass — only `public`, `protected`, and package-private members (within the same package) are inherited in an accessible way.

---

## ⚙️ How It Works

When `Dog extends Animal`, every `Dog` object actually contains, internally, a complete `Animal`'s worth of fields — inheritance isn't a "reference to" relationship, it's a genuine "is-built-with-all-of" relationship. Construction proceeds top-down: creating a `new Dog()` first runs `Animal`'s constructor (via an explicit or implicit `super()` call) to properly set up the inherited part of the object, and only after that completes does `Dog`'s own constructor body run to initialize whatever's specific to `Dog`.

```text
class Animal {
    String name;
    Animal(String name) { this.name = name; }
    void eat() { System.out.println(name + " is eating"); }
}

class Dog extends Animal {
    String breed;
    Dog(String name, String breed) {
        super(name);         // MUST run first — initializes the inherited Animal part
        this.breed = breed;  // then Dog's own field
    }
    void bark() { System.out.println(name + " says woof!"); } // "name" is inherited
}

Dog d = new Dog("Rex", "Labrador");
d.eat();   // inherited from Animal
d.bark();  // defined in Dog
```

---

## 💻 Examples

```java
class Vehicle {
    protected String make;
    protected int speed;

    public Vehicle(String make) {
        this.make = make;
        this.speed = 0;
    }

    public void accelerate(int amount) {
        speed += amount;
    }

    public void printStatus() {
        System.out.println(make + " is going " + speed + " mph");
    }
}

class Car extends Vehicle {
    private int numDoors;

    public Car(String make, int numDoors) {
        super(make);              // must be the FIRST statement
        this.numDoors = numDoors;
    }

    // Car inherits accelerate() and printStatus() automatically — no need to rewrite them
    public void honk() {
        System.out.println(make + " says beep!"); // "make" is inherited from Vehicle
    }
}

Car myCar = new Car("Toyota", 4);
myCar.accelerate(30);   // inherited method, works on Car directly
myCar.printStatus();    // "Toyota is going 30 mph"
myCar.honk();           // Car's own method
```

---

## 🚀 Real World Applications

- Modeling a natural type hierarchy — `Shape` → `Circle`/`Rectangle`/`Triangle`, or `Employee` → `Manager`/`Developer`
- Sharing common behavior (logging, validation, shared fields) across a family of related classes without duplicating code
- Building a framework or library where users extend a provided base class to plug their own behavior into an established structure
- Combining inheritance with method overriding (see [[Polymorphism - Overriding vs Overloading]]) to let subclasses customize specific pieces of inherited behavior

---

## ⚖️ Advantages

- Eliminates duplicated code across related classes by centralizing shared fields and behavior in one superclass.
- Establishes a clear, meaningful "is-a" relationship that mirrors real-world categorization and makes code easier to reason about.
- Works hand-in-hand with polymorphism, allowing code to be written generically against a superclass type while still working correctly with any specific subclass at runtime.

---

## ⚠️ Limitations

- Java's single-inheritance restriction for classes means a class can't directly inherit behavior from two unrelated class hierarchies at once — composition or interfaces are needed for that.
- Deep inheritance hierarchies (many layers of subclassing) can become difficult to trace and reason about — "where does this inherited method actually come from" becomes a real question several levels down.
- Inheritance creates tight coupling between superclass and subclass — a change to the superclass can unexpectedly ripple through every subclass depending on it, sometimes called the "fragile base class" problem.
- Overusing inheritance where composition ("has-a" instead of "is-a") would model the relationship more accurately is a very common design mistake.

---

## 🚨 Common Mistakes

- Forgetting that `super(...)` must be the very first statement in a subclass constructor — any other code before it is a compile error.
- Assuming a subclass automatically has access to a superclass's `private` fields directly — it doesn't; only inherited public/protected/package-private members are directly accessible.
- Modeling a relationship as inheritance ("is-a") when it's actually a "has-a" relationship better expressed through composition (e.g., a `Car` "has an" `Engine`, it isn't "an" `Engine`).
- Creating deep, many-level inheritance chains purely to reuse a small amount of code, when a shallower hierarchy or composition would be far easier to maintain and reason about.

---

## 📖 Further Reading

- Oracle Java Tutorials: "Inheritance"
- Oracle Java Tutorials: "Using the Keyword super"

---

## 💡 Wisdom from Mímir

Before reaching for `extends`, ask honestly: is this truly an "is-a" relationship, or does it just want to reuse some code? "A `Car` is a `Vehicle`" earns inheritance. "A `Car` needs the same logging helper a `Printer` uses" does not — that's a sign the shared logic belongs in its own class, used by both through composition instead.

---

## 🔗 Related Notes

- [[Classes and Objects]]
- [[Polymorphism - Overriding vs Overloading]]
- [[The this and super Keywords]]
- [[Abstract Classes vs Interfaces]]
- [[Java Codex]]
