---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Java
difficulty: Beginner
tags:
  - oop
  - classes
  - objects
aliases:
  - Class vs Object
  - Instantiation
publish: true
permalink: java/classes-and-objects
---

# <span class="rune">ᛟ</span> Classes and Objects

> *A class is the recipe. An object is the actual dish made from it — and you can bake as many dishes from the same recipe as you like, each one independent of the others.*

---

## 🎯 Purpose

A **class** is a blueprint defining what data (fields) and behavior (methods) something has — it doesn't itself hold any real data until instantiated. An **object** is a specific instance created from that blueprint via `new`, with its own actual values stored in memory. Every object-oriented concept in Java — inheritance, polymorphism, encapsulation — is built on top of this fundamental class/object distinction.

---

## 🧠 Key Ideas

- A class defines the **shape** of data (what fields exist) and **behavior** (what methods exist) — it's a template, not a real thing in memory by itself.
- An object is a concrete **instance** of a class, created with the `new` keyword, holding its own actual field values on the heap.
- Multiple objects created from the same class each have their own independent copy of every instance field (as covered in [[Static vs Instance Members]]), even though they share the exact same method definitions.
- A **constructor** is a special method (matching the class name, no return type) that runs automatically when `new` creates an object, responsible for initializing that object's starting state.
- The `this` keyword inside an instance method refers to "the specific object this method call is happening on" — essential when a parameter name shadows a field name of the same name.

---

## ⚙️ How It Works

Writing `class Dog { String name; }` defines the *shape* every `Dog` object will have — a `name` field — but creates no actual dog. `new Dog()` is what actually allocates memory on the heap for a real `Dog` object, running the constructor to initialize it, and returns a reference to that specific object. Every `new Dog()` call produces a genuinely separate object with its own independent `name` field, even though all `Dog` objects share the identical method code defined once in the class.

```text
class Dog {
    String name;              // instance field — a "slot" every Dog object will have

    Dog(String name) {        // constructor — runs when `new Dog(...)` executes
        this.name = name;     // "this.name" = the field; "name" (right side) = the parameter
    }

    void bark() {
        System.out.println(name + " says woof!");
    }
}

Dog a = new Dog("Rex");   // a brand-new object, its OWN "name" slot filled with "Rex"
Dog b = new Dog("Fido");  // a SEPARATE object, its OWN "name" slot filled with "Fido"
a.bark(); // "Rex says woof!"
b.bark(); // "Fido says woof!" — completely independent of a
```

---

## 💻 Examples

```java
public class Car {
    // Fields — each Car object gets its own copy of these
    String make;
    String model;
    int year;
    private int mileage;

    // Constructor
    public Car(String make, String model, int year) {
        this.make = make;      // "this.make" disambiguates the field from the parameter
        this.model = model;
        this.year = year;
        this.mileage = 0;      // default starting value, no parameter needed
    }

    // Instance method — operates on THIS object's own fields
    public void drive(int miles) {
        mileage += miles;
    }

    public int getMileage() {
        return mileage;
    }
}

// Creating and using objects
Car car1 = new Car("Toyota", "Corolla", 2022);
Car car2 = new Car("Honda", "Civic", 2023);

car1.drive(100);
car2.drive(50);

System.out.println(car1.getMileage()); // 100 — independent of car2
System.out.println(car2.getMileage()); // 50
```

---

## 🚀 Real World Applications

- Modeling any real-world entity with its own identifiable data and behavior — a `Customer`, a `BankAccount`, a `Product`
- Creating multiple independent instances that share identical behavior but maintain entirely separate state (every user session, every shopping cart, every game character)
- Structuring a program around meaningful, self-contained units of data and logic rather than loose collections of unrelated variables and functions
- Constructor overloading (see [[Constructors and Overloading]]) to support creating objects with different combinations of known initial data

---

## ⚖️ Advantages

- Bundling related data and behavior together makes code easier to reason about, test, and reuse compared to scattered variables and standalone functions.
- Each object's independent state means creating many instances never risks one instance's data accidentally colliding with another's.
- Classes provide a natural, reusable template — writing the blueprint once and instantiating it many times avoids duplicating structural code.

---

## ⚠️ Limitations

- Overusing classes for every small piece of data can add unnecessary structural overhead compared to simpler data-holding approaches (like a `record`, for genuinely simple immutable data) when full object behavior isn't actually needed.
- A class with too many responsibilities becomes difficult to maintain — a well-designed class should generally represent one clear, cohesive concept.
- Forgetting to properly initialize fields in a constructor can leave an object in an inconsistent or invalid state that later code assumes won't happen.

---

## 🚨 Common Mistakes

- Confusing a class with an object — trying to access fields directly on the class name instead of on a specific instance for non-static members.
- Forgetting `this.fieldName = parameterName;` inside a constructor when the parameter and field share the same name, causing the parameter to simply reassign itself and leave the actual field uninitialized.
- Not providing any constructor and being surprised fields default to `0`/`null`/`false` rather than any meaningful starting value.
- Creating far too many tiny, overly granular classes (or, at the other extreme, one enormous class doing everything) instead of finding a cohesive, well-scoped responsibility boundary.

---

## 📖 Further Reading

- Oracle Java Tutorials: "Classes and Objects"
- Oracle Java Tutorials: "Defining Methods"

---

## 💡 Wisdom from Mímir

The instant a constructor parameter shares a name with a field, `this.` stops being optional style and becomes load-bearing — without it, `name = name;` just reassigns the parameter to itself, and the actual field silently stays at its default value. Check for this specific pattern first whenever a newly constructed object's fields look wrong.

---

## 🔗 Related Notes

- [[Constructors and Overloading]]
- [[Encapsulation]]
- [[Static vs Instance Members]]
- [[Java Codex]]
