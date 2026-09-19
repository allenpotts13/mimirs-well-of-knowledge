---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Java
difficulty: Beginner
tags:
  - java-fundamentals
  - access-modifiers
  - encapsulation
aliases:
  - Public Private Protected
  - Package-Private
publish: true
permalink: java/access-modifiers
---

# <span class="rune">ᛟ</span> Access Modifiers

> *Java has four access levels, but only three keywords — the fourth, the most restrictive default of all, is what you get by simply writing nothing.*

---

## 🎯 Purpose

Access modifiers control which other classes are allowed to see and use a given field, method, constructor, or class — the mechanical enforcement behind [[Encapsulation|encapsulation]]. Java has four distinct access levels, but the one most people forget exists is the unnamed default: leaving off a modifier entirely doesn't mean "public," it means something more restrictive called **package-private**.

---

## 🧠 Key Ideas

- `public` — accessible from anywhere, in any class, in any package.
- `protected` — accessible within the same package, AND from subclasses in other packages (specifically through inheritance, not just any code that imports the class).
- *(no modifier / default)* — **package-private**: accessible only to other classes within the same package; this is what you get by simply omitting a modifier.
- `private` — accessible only within the same class itself; not even subclasses can see it directly.
- The access levels form a strict hierarchy from most to least restrictive: `private` → *(package-private)* → `protected` → `public`.

---

## ⚙️ How It Works

The compiler enforces every access check at compile time, based purely on where the accessing code lives relative to the declaring class — not on anything about the runtime object. `protected` is the one level that surprises people most: it grants access to the *entire package* (like package-private does) **plus** any subclass anywhere, even in a completely different package — but critically, a subclass in another package can only access the protected member *through inheritance* (via `this` or an instance of itself/a further subclass), not through an arbitrary instance of the original class it doesn't otherwise have visibility into.

```text
public class Animal {
    public String name;        // any class, anywhere
    protected int age;         // same package + any subclass anywhere
    int legCount;               // package-private — same package only
    private String secretId;    // this class ONLY
}
```

---

## 💻 Examples

```java
package animals;

public class Animal {
    public String name;
    protected int age;
    int legCount;          // package-private (no modifier)
    private String id;

    private void generateId() {   // only callable from within Animal itself
        id = "ANIMAL-" + System.nanoTime();
    }
}
```

```java
package animals;

class Zoo {                        // same package — sees name, age, legCount, but NOT id
    void inspect(Animal a) {
        System.out.println(a.name);      // OK — public
        System.out.println(a.age);       // OK — same package
        System.out.println(a.legCount);  // OK — same package
        // System.out.println(a.id);     // ERROR — private, not accessible
    }
}
```

```java
package habitat;

import animals.Animal;

public class Dog extends Animal {  // subclass in a DIFFERENT package
    void checkAge() {
        System.out.println(age);      // OK — protected, accessible via inheritance
        // System.out.println(legCount); // ERROR — package-private, different package
    }
}
```

---

## 🚀 Real World Applications

- Making fields `private` and exposing controlled access through public getter/setter methods — the standard [[Encapsulation]] pattern
- Using `protected` for fields or methods meant to be extended and customized by subclasses, but not exposed to unrelated external code
- Leaving helper classes or methods package-private when they're implementation details meant only for other classes in the same module, never intended for outside use
- Designing a public API surface deliberately small (`public`) while keeping internal machinery `private` or package-private, so implementation details can change freely without breaking external code

---

## ⚖️ Advantages

- Fine-grained control over exactly what's exposed lets a class present a clean public API while hiding messy or fragile implementation details.
- Package-private provides a useful middle ground for code that needs to collaborate closely within a module without exposing that coupling to the outside world.
- `protected` specifically supports designing extensible class hierarchies, giving subclasses the access they need without opening that access to unrelated code.

---

## ⚠️ Limitations

- Package-private access depends entirely on physical package structure — reorganizing packages during a refactor can unexpectedly break access that was silently relying on same-package visibility.
- Overusing `public` "just to be safe" defeats the purpose of encapsulation entirely, exposing internal details that should have been hidden and making future changes far riskier.
- `protected`'s exact rules (package-level access, PLUS subclass access, but only via inheritance for out-of-package subclasses) are genuinely one of the more confusing details in the language and are easy to misremember.

---

## 🚨 Common Mistakes

- Forgetting that omitting an access modifier entirely does NOT mean public — it means package-private, a distinct and more restrictive level that beginners frequently overlook exists at all.
- Making every field `public` to avoid dealing with getters/setters, defeating encapsulation and making the class's invariants impossible to protect or enforce.
- Assuming `protected` grants full access to any code that simply imports the class — it specifically requires either same-package access or a genuine subclass relationship, not just visibility of the type.
- Making a field `private` when it actually needs to be accessed by a subclass, then working around it awkwardly instead of using `protected` (or a proper protected getter) as intended.

---

## 📖 Further Reading

- Oracle Java Tutorials: "Controlling Access to Members of a Class"

---

## 💡 Wisdom from Mímir

Default to the most restrictive access level that still does the job, and loosen it only when a real, specific need arises — `private` first, then package-private, then `protected`, and `public` only for the deliberate, minimal surface you actually intend to support. Tightening access later, after other code has come to depend on looser access, is far more disruptive than starting tight.

---

## 🔗 Related Notes

- [[Encapsulation]]
- [[Static vs Instance Members]]
- [[Packages and Imports]]
- [[Java Codex]]
