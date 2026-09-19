---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Java
difficulty: Intermediate
tags:
  - generics
  - type-safety
  - type-erasure
aliases:
  - Generic Classes
  - Generic Methods
  - Type Erasure
publish: true
permalink: java/generics-and-type-parameters
---

# <span class="rune">ᛟ</span> Generics and Type Parameters

> *Generics feel like they add real type information at runtime — but by the time your code is actually executing, every bit of that information has already been erased. It only ever existed for the compiler's benefit.*

---

## 🎯 Purpose

Generics let classes, interfaces, and methods operate on a **type parameter** — a placeholder for a specific type, decided when the generic type is actually used — providing compile-time type safety without duplicating code for every possible type. Before generics (pre-Java 5), collections held plain `Object` references, requiring manual, error-prone casting everywhere; generics eliminated that entire category of `ClassCastException` risk at the cost of one new concept to understand: type erasure.

---

## 🧠 Key Ideas

- A type parameter, conventionally a single capital letter (`T` for Type, `E` for Element, `K`/`V` for Key/Value), acts as a placeholder filled in with a concrete type when the generic class/method is actually used: `List<String>`, `Map<String, Integer>`.
- Generic classes are declared with the type parameter in angle brackets: `class Box<T> { T value; }` — every method inside can reference `T` as if it were a real type.
- Generic methods can introduce their own type parameter independent of the class they're in: `<T> T firstElement(List<T> list)`.
- **Type erasure** means generic type information exists only at compile time — the compiler uses it to check type safety, then erases it, replacing `T` with `Object` (or a bound type) in the actual compiled bytecode; at runtime, a `List<String>` and a `List<Integer>` are genuinely indistinguishable.
- **Bounded type parameters** (`<T extends Number>`) restrict what types can be substituted, and **wildcards** (`<? extends T>`, `<? super T>`) provide flexibility when working with generic types whose exact parameter isn't fully known — both covered in depth in [[Bounded Type Parameters and Wildcards]].

---

## ⚙️ How It Works

At compile time, the compiler treats `Box<String>` as if `T` were literally `String` everywhere inside `Box`, checking every assignment and method call against that substitution — this is where all of generics' actual type safety comes from. Once compilation succeeds, the compiler performs **type erasure**: it strips out the generic type information entirely, replacing type parameters with their bound (`Object` if unbounded) and inserting the casts that would have been written by hand in the pre-generics era automatically, invisibly, at the exact points where they're needed. This is why `List<String>.class` and `List<Integer>.class` are the same `.class` file at runtime — the distinction only ever existed for the compiler.

```text
// What you write:
List<String> names = new ArrayList<>();
names.add("Alice");
String first = names.get(0);

// What effectively exists after type erasure, roughly:
List names = new ArrayList();       // raw type — T erased to Object
names.add("Alice");
String first = (String) names.get(0); // compiler inserts this cast automatically
```

---

## 💻 Examples

```java
// Generic class
class Box<T> {
    private T value;
    public void set(T value) { this.value = value; }
    public T get() { return value; }
}

Box<String> stringBox = new Box<>();
stringBox.set("Hello");
String s = stringBox.get(); // no cast needed — compiler already knows it's a String

Box<Integer> intBox = new Box<>();
intBox.set(42);
// intBox.set("Oops"); // COMPILE ERROR — type safety enforced

// Generic method — its own type parameter, independent of any enclosing class
public static <T> T firstElement(List<T> list) {
    return list.get(0);
}

String first = firstElement(List.of("a", "b", "c"));   // T inferred as String
Integer firstNum = firstElement(List.of(1, 2, 3));      // T inferred as Integer

// Multiple type parameters
class Pair<K, V> {
    private K key;
    private V value;
    public Pair(K key, V value) { this.key = key; this.value = value; }
    public K getKey() { return key; }
    public V getValue() { return value; }
}

Pair<String, Integer> entry = new Pair<>("age", 30);

// Type erasure in action — this doesn't compile, because BOTH are just List at runtime
// void overload(List<String> a) {}
// void overload(List<Integer> a) {} // ERROR: erasure produces an identical signature
```

---

## 🚀 Real World Applications

- Every use of the Collections Framework (`List<T>`, `Map<K,V>`, `Set<T>`) relies entirely on generics for compile-time type safety
- Writing reusable container or utility classes (`Box<T>`, `Pair<K,V>`, `Result<T>`) that work identically regardless of the specific type they hold
- Writing generic algorithms (a sorting method, a search method) that work on any comparable type without duplicating the logic per type
- Understanding library method signatures that use bounded type parameters and wildcards, covered further in [[Bounded Type Parameters and Wildcards]]

---

## ⚖️ Advantages

- Compile-time type checking eliminates an entire historical category of `ClassCastException` bugs from the pre-generics era of raw `Object`-based collections.
- Generic code is written once and works correctly for any type substitution, avoiding duplicated type-specific versions of the same logic.
- IDE support (autocomplete, type checking) is dramatically better with generics than with raw, untyped `Object`-based code.

---

## ⚠️ Limitations

- Type erasure means generic type information is completely unavailable at runtime — you cannot check `if (list instanceof List<String>)` or create an array of a generic type parameter (`new T[10]`) directly.
- Two methods that would only differ by their generic type parameter after erasure (like the commented-out `overload` example above) cannot coexist — this is a genuine, sometimes-surprising compile error.
- Generics don't work with primitive types directly — `List<int>` doesn't compile; the wrapper class (`List<Integer>`) must be used instead, relying on autoboxing (see [[Primitive Types vs Reference Types]]).

---

## 🚨 Common Mistakes

- Trying to check an object's generic type parameter at runtime (`if (obj instanceof List<String>)`), which doesn't compile — type erasure means only the raw type (`List`) is checkable at runtime, never the specific type argument.
- Attempting to create a generic array directly (`new T[10]`) inside a generic class — this doesn't compile due to type erasure and requires a workaround (like creating an `Object[]` and casting, with an accompanying unchecked-cast warning).
- Using raw types (`List` instead of `List<String>`) out of old habit or unfamiliarity, losing all of generics' compile-time safety and reintroducing the exact `ClassCastException` risk generics exist to prevent.
- Overloading methods that only differ in their generic type argument after erasure, not realizing the compiler sees them as having identical signatures.

---

## 📖 Further Reading

- Oracle Java Tutorials: "Generics"
- Oracle Java Tutorials: "Type Erasure"

---

## 💡 Wisdom from Mímir

Whenever generics behave in a way that seems to contradict "but the compiler knew the type right there," the explanation is almost always type erasure — that type information is a compile-time-only convenience, gone completely by the time the program actually runs. Internalizing that one fact resolves most of generics' more confusing edge cases immediately.

---

## 🔗 Related Notes

- [[Bounded Type Parameters and Wildcards]]
- [[The Collections Framework Overview]]
- [[Primitive Types vs Reference Types]]
- [[Java Codex]]
