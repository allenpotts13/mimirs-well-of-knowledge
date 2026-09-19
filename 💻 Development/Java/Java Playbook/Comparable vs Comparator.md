---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Java
difficulty: Intermediate
tags:
  - collections
  - sorting
  - comparable
  - comparator
aliases:
  - compareTo Method
  - Custom Sorting Java
publish: true
permalink: java/comparable-vs-comparator
---

# <span class="rune">ᛟ</span> Comparable vs Comparator

> *Comparable asks a class to define its own single natural order, once, forever. Comparator lets anyone define as many different orderings as they want, from entirely outside the class, without ever touching its source.*

---

## 🎯 Purpose

Both interfaces answer the same fundamental question — "which of these two objects comes first?" — but from opposite directions. `Comparable<T>` is implemented *by* the class itself, defining one single, intrinsic "natural" ordering. `Comparator<T>` is a separate, standalone object that defines a comparison rule *externally*, letting the same class be sorted in as many different ways as needed without ever modifying the class.

---

## 🧠 Key Ideas

- `Comparable<T>` declares one method: `compareTo(T other)`, returning negative (this comes first), zero (equal), or positive (this comes after) — implemented directly inside the class being compared.
- A class can implement `Comparable` only **once** — it defines a single natural ordering, baked into the class itself.
- `Comparator<T>` declares `compare(T a, T b)` with the same negative/zero/positive convention, but lives as a completely separate object — any number of different `Comparator`s can exist for the same class, each defining a different ordering.
- `Collections.sort(list)` and `Collections.sort(list, comparator)` (or the equivalent `list.sort(...)`) use `Comparable`'s natural order in the first form, and an explicit `Comparator` in the second — overriding the natural order for that specific sort call.
- Modern `Comparator` usage favors composable static/default methods — `Comparator.comparing(...)`, `.thenComparing(...)`, `.reversed()` — over hand-writing the full `compare()` method body.

---

## ⚙️ How It Works

`TreeMap`/`TreeSet` (see [[HashMap vs TreeMap vs LinkedHashMap]] and [[HashSet vs TreeSet vs LinkedHashSet]]) and sorting methods like `Collections.sort()` all rely on *some* comparison rule to determine order — by default, they call `compareTo()` on the elements themselves, meaning those elements must implement `Comparable`. Supplying a `Comparator` instead overrides that default entirely for that specific call, without requiring the element class to implement anything at all — this is precisely why you can sort a class you don't own (and can't modify to add `Comparable`) by supplying an external `Comparator`.

```text
class Person implements Comparable<Person> {
    public int compareTo(Person other) { return this.age - other.age; }  // ONE natural order: by age
}

Comparator<Person> byName = Comparator.comparing(p -> p.name);      // an ADDITIONAL, external order
Comparator<Person> byNameThenAge = byName.thenComparing(p -> p.age); // composed further
```

---

## 💻 Examples

```java
class Person implements Comparable<Person> {
    String name;
    int age;

    Person(String name, int age) { this.name = name; this.age = age; }

    @Override
    public int compareTo(Person other) {
        return Integer.compare(this.age, other.age); // natural order: by age, ascending
    }

    @Override
    public String toString() { return name + "(" + age + ")"; }
}

List<Person> people = new ArrayList<>(List.of(
    new Person("Charlie", 35),
    new Person("Alice", 30),
    new Person("Bob", 25)
));

Collections.sort(people); // uses Comparable's natural order (by age)
System.out.println(people); // [Bob(25), Alice(30), Charlie(35)]

// Comparator — sort by name instead, without touching the Person class at all
people.sort(Comparator.comparing(p -> p.name));
System.out.println(people); // [Alice(30), Bob(25), Charlie(35)]

// Composable comparators — sort by age descending, then by name ascending as a tiebreaker
people.sort(
    Comparator.comparingInt((Person p) -> p.age)
        .reversed()
        .thenComparing(p -> p.name)
);
System.out.println(people); // [Charlie(35), Alice(30), Bob(25)]

// A one-off Comparator using a lambda, without Comparator.comparing() helpers
Comparator<Person> byNameLength = (a, b) -> a.name.length() - b.name.length();
people.sort(byNameLength);
```

---

## 🚀 Real World Applications

- Implementing `Comparable` on a domain class (`Employee`, `Product`) to give it one obvious, universal default sort order (e.g., by ID or by name)
- Using `Comparator` to sort the same class differently in different contexts (a `Person` list sorted by age in one report, by name in another) without ever modifying `Person` itself
- Sorting a third-party or library class that you can't modify to implement `Comparable`, by supplying an external `Comparator` instead
- Using `TreeMap`/`TreeSet` with a custom `Comparator` to get a sort order other than a class's own natural `Comparable` ordering

---

## ⚖️ Advantages

- `Comparable` provides a sensible, discoverable default ordering built directly into the class, requiring no extra objects for the common case.
- `Comparator` allows unlimited alternative orderings without needing to modify (or even have access to) the original class's source code.
- Modern composable `Comparator` methods (`comparing`, `thenComparing`, `reversed`) make building complex multi-field sort orders concise and highly readable compared to hand-written `compare()` logic.

---

## ⚠️ Limitations

- A class can only have one `Comparable` natural order — any additional orderings genuinely require separate `Comparator` objects.
- `compareTo()` and `equals()` are expected to be **consistent** by convention (though not strictly enforced by the compiler) — if `a.compareTo(b) == 0` but `a.equals(b)` is `false`, certain collections (notably `TreeSet`/`TreeMap`, which use `compareTo()` for equality checks internally) can behave in ways that contradict what `equals()` alone would suggest.
- Hand-writing a `compare()` method with subtraction (`a.age - b.age`) risks integer overflow for extreme values — `Integer.compare(a, b)` (or the modern `Comparator.comparingInt` helpers) avoids this pitfall entirely.

---

## 🚨 Common Mistakes

- Writing `return a.age - b.age;` inside a `compareTo()`/`compare()` implementation, which can silently overflow and produce an incorrect result for very large or very negative values — `Integer.compare(a.age, b.age)` is the safe alternative.
- Implementing `Comparable` inconsistently with `equals()` and being surprised when a `TreeSet` treats two "unequal" (by `.equals()`) objects as duplicates because their `compareTo()` returned zero.
- Forgetting that `Comparator.comparing(...)` composed with `.thenComparing(...)` evaluates left to right — putting the tiebreaker field first by mistake and getting an unintended primary sort order.
- Trying to add `Comparable` to a class after the fact when a `Comparator` would be simpler and less invasive, especially for a class already used and sorted in multiple different ways throughout a codebase.

---

## 📖 Further Reading

- Oracle documentation: `java.lang.Comparable`, `java.util.Comparator` interface references
- Oracle Java Tutorials: "Object Ordering"

---

## 💡 Wisdom from Mímir

If a class has one obvious default sort order that everyone would expect (like sorting `Employee` by ID), give it `Comparable`. The moment a second legitimate ordering is needed — sort by name here, sort by hire date there — stop trying to force `Comparable` to do double duty and reach for a `Comparator` instead, one per ordering, kept wherever it's actually used.

---

## 🔗 Related Notes

- [[HashMap vs TreeMap vs LinkedHashMap]]
- [[HashSet vs TreeSet vs LinkedHashSet]]
- [[Object Equality - equals(), ==, and hashCode()]]
- [[Java Codex]]
