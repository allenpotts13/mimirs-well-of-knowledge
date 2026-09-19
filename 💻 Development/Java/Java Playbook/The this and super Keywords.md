---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Java
difficulty: Beginner
tags:
  - oop
  - this-keyword
  - super-keyword
aliases:
  - this() vs super()
  - Referencing the Current Object
publish: true
permalink: java/the-this-and-super-keywords
---

# <span class="rune">ᛟ</span> The this and super Keywords

> *`this` points to the object you're standing inside of right now. `super` points to what that object was built on top of. Both let you reach for something that isn't directly in front of you in the current scope.*

---

## 🎯 Purpose

`this` and `super` are two related but distinct reference keywords: `this` refers to the current object instance a method or constructor is executing on, and `super` refers to the immediate superclass of the current class — used to access an overridden method's original version or to invoke a superclass constructor. Both resolve a common ambiguity problem: when a local variable, parameter, or overriding method shares a name with something inherited or belonging to the current object, `this`/`super` disambiguate exactly which one is meant.

---

## 🧠 Key Ideas

- `this.fieldName` explicitly refers to the current object's own field — essential when a constructor or method parameter shares a name with a field (see [[Classes and Objects]]).
- `this(...)` (as the first line of a constructor) calls another constructor in the *same* class — constructor chaining, covered in [[Constructors and Overloading]].
- `super.methodName()` calls the superclass's version of a method that the current class has overridden — used to extend rather than fully replace inherited behavior.
- `super(...)` (as the first line of a constructor) calls a constructor in the superclass — required (explicitly or implicitly) before a subclass constructor can run its own initialization.
- Neither `this` nor `super` can be used inside a `static` context (a static method or static initializer) — there's no "current object" for a static method to refer to at all.

---

## ⚙️ How It Works

`this` is implicitly available inside every instance method and constructor, silently referring to whichever specific object the method call is currently executing on — it only needs to be written explicitly when there's a naming conflict (like a constructor parameter shadowing a field) or when passing the current object itself somewhere as an argument. `super` works similarly but shifts the reference up one level in the inheritance chain — `super.method()` specifically bypasses the current class's own overriding implementation and reaches directly for the superclass's version instead.

```text
class Animal {
    void speak() { System.out.println("Generic sound"); }
}

class Dog extends Animal {
    @Override
    void speak() {
        super.speak();                    // calls Animal's ORIGINAL speak(), not Dog's own
        System.out.println("Woof!");      // then adds Dog's own behavior
    }
}

new Dog().speak();
// Output:
// Generic sound
// Woof!
```

---

## 💻 Examples

```java
class Person {
    protected String name;

    public Person(String name) {
        this.name = name;           // disambiguates field from parameter
    }

    public void introduce() {
        System.out.println("I am " + name);
    }
}

class Student extends Person {
    private String school;

    public Student(String name, String school) {
        super(name);                 // MUST be first — calls Person's constructor
        this.school = school;
    }

    public Student(String name) {
        this(name, "Unknown");       // constructor chaining — this() must be a constructor's first line
    }

    @Override
    public void introduce() {
        super.introduce();           // reuses Person's version...
        System.out.println("...and I study at " + school); // ...then adds more
    }
}

new Student("Alice", "MIT").introduce();
// Output:
// I am Alice
// ...and I study at MIT

// Passing "this" — the current object — as an argument
class EventListener {
    void register(Button b) {
        b.setListener(this); // passes THIS specific EventListener instance to the Button
    }
}
```

---

## 🚀 Real World Applications

- Disambiguating constructor/setter parameters from fields of the same name, a nearly universal pattern in real Java code
- Extending an overridden method's behavior with `super.method()` instead of fully duplicating the superclass's logic
- Chaining constructors within the same class via `this(...)` to avoid duplicated initialization code
- Passing the current object (`this`) to another object as a callback reference, a common pattern in event-driven and listener-based designs

---

## ⚠️ Limitations

- Neither keyword works in a static context — a common early mistake when a beginner tries to use `this` inside `main()` or another static method.
- `this(...)` and `super(...)` are mutually exclusive as the first line of a constructor — a constructor can chain to another constructor in the same class, OR call the superclass, but not both from the same constructor body.
- Overusing `this.` for every single field reference (even where there's no naming conflict at all) adds unnecessary verbosity without any functional benefit — it's most valuable specifically when disambiguation is actually needed.

---

## 🚨 Common Mistakes

- Forgetting `this.` when a constructor parameter shares a name with a field, silently leaving the actual field at its default value while the parameter just reassigns itself.
- Trying to use `this` or `super` inside a `static` method, which doesn't compile — there's no current object instance for either keyword to refer to.
- Attempting to call both `this(...)` and `super(...)` in the same constructor — only one can be the (single) first statement.
- Calling `super.method()` reflexively without actually needing the superclass's original behavior, when the override was intended to fully replace it instead of extend it.

---

## 📖 Further Reading

- Oracle Java Tutorials: "Using the this Keyword"
- Oracle Java Tutorials: "Using the Keyword super"

---

## 💡 Wisdom from Mímir

Treat `this.` as load-bearing exactly once a parameter or local variable shadows a field name — everywhere else, it's a matter of style. `super.method()`, by contrast, is a deliberate design choice every time: reach for it only when the override genuinely means to extend inherited behavior, not replace it outright.

---

## 🔗 Related Notes

- [[Classes and Objects]]
- [[Constructors and Overloading]]
- [[Inheritance and the extends Keyword]]
- [[Java Codex]]
