---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Java
difficulty: Intermediate
tags:
  - oop
  - polymorphism
  - method-overriding
aliases:
  - Dynamic Dispatch
  - Runtime Polymorphism
  - @Override
publish: true
permalink: java/polymorphism-overriding-vs-overloading
---

# <span class="rune">ᛟ</span> Polymorphism - Overriding vs Overloading

> *Overloading is decided at compile time by what you typed. Overriding is decided at runtime by what the object actually is — and mixing up which one is happening is the source of nearly every "why did the wrong method run" surprise in inheritance-heavy code.*

---

## 🎯 Purpose

Polymorphism — "many forms" — lets code written against a general supertype work correctly with any specific subtype at runtime, automatically calling the *most specific* version of an overridden method for whatever actual object is involved. This is achieved through **method overriding**: a subclass providing its own implementation of a method already defined in its superclass, with the exact same signature. It's easy to confuse with [[Constructors and Overloading|overloading]] — the names sound alike, but the two mechanisms work in completely different ways.

---

## 🧠 Key Ideas

- **Overriding** happens between a superclass and subclass — the subclass redefines a method with the *exact same* name, parameters, and return type (or a covariant subtype), and the subclass's version replaces the superclass's when called.
- **Overloading** happens within the *same* class — multiple methods share a name but have *different* parameter lists, resolved entirely at compile time (see [[Constructors and Overloading]]).
- Which overridden method actually runs is decided at **runtime**, based on the object's actual type — this is called **dynamic dispatch**, and it's what makes polymorphism work.
- The `@Override` annotation isn't required, but it tells the compiler to verify the method genuinely overrides a superclass method — catching typos (a slightly wrong parameter list that would otherwise silently create an unrelated overload instead of an override) at compile time.
- `super.methodName()` inside an overriding method calls the *superclass's* version of that same method — useful for extending, rather than completely replacing, inherited behavior.

---

## ⚙️ How It Works

When code holds a reference typed as a supertype (`Animal a = new Dog();`) and calls an overridden method (`a.makeSound();`), Java doesn't look at the *reference's* declared type to decide which version runs — it looks at the *actual object's* real runtime type. Because `a` actually refers to a `Dog` object, `Dog`'s overridden `makeSound()` runs, even though `a` is declared as `Animal`. This is fundamentally different from overloading, where the compiler picks a specific overload once, permanently, at compile time, based purely on the argument types visible in the source code.

```text
Animal a = new Dog();
a.makeSound();     // runtime looks at the ACTUAL object (Dog), calls Dog's makeSound()
                    // even though "a" is declared as type Animal — this is dynamic dispatch

// Compare to overloading — resolved at COMPILE time, based on declared argument types:
mathHelper.add(1, 2);      // compiler picks add(int, int) — decided before the program even runs
```

---

## 💻 Examples

```java
class Animal {
    void makeSound() {
        System.out.println("Some generic animal sound");
    }
}

class Dog extends Animal {
    @Override
    void makeSound() {           // OVERRIDING — same signature, replaces the superclass version
        System.out.println("Woof!");
    }
}

class Cat extends Animal {
    @Override
    void makeSound() {
        System.out.println("Meow!");
        super.makeSound();       // still calls Animal's original version too, if desired
    }
}

// Polymorphism in action
List<Animal> animals = List.of(new Dog(), new Cat(), new Animal());
for (Animal a : animals) {
    a.makeSound();  // calls the CORRECT version for each actual object, decided at runtime
}
// Output:
// Woof!
// Meow!
// Some generic animal sound

// @Override catches mistakes at compile time
class Bird extends Animal {
    @Override
    void makeSund() {  // TYPO — compiler ERROR because of @Override: "method does not override..."
        System.out.println("Tweet!");
    }
    // Without @Override, this would silently compile as an unrelated NEW method instead
}
```

---

## 🚀 Real World Applications

- Writing a method that accepts a supertype parameter and works correctly with any subtype passed in, without needing to know which specific subtype it is
- Building extensible frameworks where users override specific "hook" methods to customize behavior while the framework calls them generically
- Processing a heterogeneous collection (a `List<Shape>` containing circles, squares, and triangles) and calling `area()` polymorphically on each without type-checking every element
- Extending inherited behavior with `super.method()` rather than duplicating the superclass's logic

---

## ⚖️ Advantages

- Enables writing general-purpose code against a supertype that automatically works correctly with any current or future subtype.
- `@Override` provides a compile-time safety net against accidental overload-instead-of-override typos, one of the most common inheritance bugs.
- Dynamic dispatch is what makes design patterns like Strategy, Template Method, and many framework extension points possible at all.

---

## ⚠️ Limitations

- Static methods, `private` methods, and fields are **not** polymorphic — they're resolved based on the declared reference type, not the runtime object, which surprises people expecting all inherited members to behave the same way.
- Deep override chains across many levels of inheritance can make it hard to trace which specific version of a method actually executes without careful reading.
- Overriding without fully understanding the superclass's contract (its documented behavior and invariants) can silently break code that depends on that contract being honored.

---

## 🚨 Common Mistakes

- Forgetting `@Override` and making a small typo in the method signature, which silently creates a brand-new, unrelated overloaded method instead of actually overriding the intended one — with no compiler warning at all.
- Confusing overriding (runtime, inheritance-based, same signature) with overloading (compile-time, same-class, different signature) — they solve entirely different problems despite the similar names.
- Assuming a `static` method can be "overridden" polymorphically — static methods are resolved by the reference's declared type at compile time, not the object's actual runtime type, so this doesn't work the way instance method overriding does.
- Changing an overridden method's behavior in a way that violates what callers of the superclass type reasonably expect, breaking the Liskov Substitution Principle even though the code still compiles fine.

---

## 📖 Further Reading

- Oracle Java Tutorials: "Overriding and Hiding Methods"
- Oracle Java Tutorials: "Polymorphism"

---

## 💡 Wisdom from Mímir

Make `@Override` a reflex on every single method meant to override a superclass method, with zero exceptions — it costs nothing when you're right, and it's the only thing standing between a subtle typo and a bug that silently does the wrong thing with no compiler error to point you toward it.

---

## 🔗 Related Notes

- [[Inheritance and the extends Keyword]]
- [[Constructors and Overloading]]
- [[Abstract Classes vs Interfaces]]
- [[Java Codex]]
