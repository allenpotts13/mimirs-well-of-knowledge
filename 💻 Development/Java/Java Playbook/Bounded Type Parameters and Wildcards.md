---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Java
difficulty: Advanced
tags:
  - generics
  - wildcards
  - pecs-principle
aliases:
  - PECS Principle
  - extends vs super Wildcard
publish: true
permalink: java/bounded-type-parameters-and-wildcards
---

# <span class="rune">ᛟ</span> Bounded Type Parameters and Wildcards

> *PECS — "Producer Extends, Consumer Super" — is the one mnemonic that turns wildcard confusion into a mechanical decision: if it hands you values, use extends; if you hand it values, use super.*

---

## 🎯 Purpose

Plain generics (`<T>`) accept *any* type with no restriction at all — but often a generic method genuinely needs to constrain what's allowed (only numbers, only comparable types) or needs flexibility when working with a generic type whose exact parameter isn't fully known at the call site. **Bounded type parameters** (`<T extends Number>`) solve the first problem; **wildcards** (`<? extends T>`, `<? super T>`) solve the second — together they're consistently one of the more advanced and exam-relevant corners of Java generics.

---

## 🧠 Key Ideas

- A **bounded type parameter** (`<T extends Number>`) restricts a generic type to `Number` or any of its subtypes, and — crucially — lets the method body call `Number`'s methods (like `.doubleValue()`) directly on values of type `T`, which plain unbounded `<T>` would never allow.
- Despite the keyword `extends`, a bounded type parameter uses `extends` for **both** class bounds and interface bounds (`<T extends Comparable<T>>`) — `implements` is never used in this position.
- A **wildcard** (`?`) represents an *unknown* specific type, used in a variable's type declaration rather than a type parameter definition — `List<?>` means "a list of some specific type, we just don't know or care which."
- `<? extends T>` (an **upper-bounded wildcard**) means "a list of T or any subtype" — safe to *read* from (every element is guaranteed at least a `T`), but unsafe to *add* to (the compiler can't guarantee what specific subtype is actually expected).
- `<? super T>` (a **lower-bounded wildcard**) means "a list of T or any supertype" — safe to *add* `T` instances to (any supertype can hold a `T`), but reading only guarantees you get back an `Object`.

---

## ⚙️ How It Works

The mnemonic **PECS — Producer Extends, Consumer Super** — captures the entire practical decision: if a generic parameter is a source you're only reading values *out of* (a producer), use `<? extends T>`; if it's a destination you're only writing values *into* (a consumer), use `<? super T>`. This isn't an arbitrary rule — it directly reflects what the compiler can and can't guarantee: with `List<? extends Number>`, the compiler knows every element is *at least* a `Number` (safe to read as `Number`), but has no idea if the actual list is a `List<Integer>` or `List<Double>` (so adding *any* specific type, even a `Number` itself, could violate the list's real, hidden type — hence writes are disallowed).

```text
List<? extends Number> producer = List.of(1, 2, 3);
Number n = producer.get(0);      // SAFE — guaranteed at least a Number
// producer.add(5);              // COMPILE ERROR — could be a List<Integer>, List<Double>, unknown which

List<? super Integer> consumer = new ArrayList<Number>();
consumer.add(5);                  // SAFE — any supertype of Integer can hold an Integer
Object o = consumer.get(0);       // only guaranteed to be an Object — the real type is unknown
```

---

## 💻 Examples

```java
// Bounded type parameter — restricts T to Number and its subtypes
public static <T extends Number> double sum(List<T> list) {
    double total = 0;
    for (T item : list) {
        total += item.doubleValue();  // ONLY possible because T is bounded to Number
    }
    return total;
}

sum(List.of(1, 2, 3));         // works — Integer extends Number
sum(List.of(1.5, 2.5));        // works — Double extends Number
// sum(List.of("a", "b"));     // COMPILE ERROR — String is not a Number

// Multiple bounds — a class bound plus interface bounds
public static <T extends Number & Comparable<T>> T max(List<T> list) {
    T max = list.get(0);
    for (T item : list) {
        if (item.compareTo(max) > 0) max = item;
    }
    return max;
}

// Wildcards — PECS in practice
public static double sumWildcard(List<? extends Number> list) { // PRODUCER — only reading
    double total = 0;
    for (Number n : list) total += n.doubleValue();
    return total;
}

public static void addIntegers(List<? super Integer> list) {    // CONSUMER — only writing
    list.add(1);
    list.add(2);
}

List<Integer> ints = new ArrayList<>();
addIntegers(ints);                    // Integer's own list — fine
List<Number> nums = new ArrayList<>();
addIntegers(nums);                    // ALSO fine — Number is a supertype of Integer

// Unbounded wildcard — genuinely don't care about the type at all
public static void printSize(List<?> list) {
    System.out.println("Size: " + list.size()); // no element access needed — size() works regardless
}
```

---

## 🚀 Real World Applications

- Writing a generic utility method that needs to call numeric methods (`.doubleValue()`, arithmetic) on its type parameter, requiring a `<T extends Number>` bound
- Designing flexible API methods that accept "a list of this type or any subtype" for reading (`Collections.copy`, `Collections.max` in the standard library both use bounded wildcards extensively)
- Writing a method that fills a provided collection with new elements, accepting "this type or any supertype" so callers can pass in a more general collection
- Constraining a generic type to require `Comparable` support (`<T extends Comparable<T>>`) so the method body can safely call `compareTo()`

---

## ⚖️ Advantages

- Bounded type parameters let generic code call meaningful methods on the type parameter that plain unbounded generics would never allow, without sacrificing type safety.
- Wildcards make API methods significantly more flexible and reusable, accepting a wider range of compatible generic types than an exact match would allow.
- PECS gives a simple, mechanical rule for choosing between `extends` and `super` wildcards, removing what would otherwise be genuine guesswork.

---

## ⚠️ Limitations

- Wildcard syntax and rules are widely considered one of the more genuinely difficult parts of Java to internalize — the "why can't I add to this list" restriction on `<? extends T>` trips up even experienced developers revisiting it after time away.
- Multiple bounds (`<T extends Number & Comparable<T>>`) can only include **one** class bound (which must come first) but multiple interface bounds — this specific ordering rule is easy to get wrong.
- Overusing wildcards in method signatures where a simple bounded type parameter would communicate the same intent more clearly can make an API harder to read than necessary.

---

## 🚨 Common Mistakes

- Trying to `.add()` to a `List<? extends T>`, not realizing the compiler correctly refuses this because it can't guarantee what the collection's real underlying type actually is.
- Using `<? super T>` when the code only ever reads from the collection, or `<? extends T>` when it only ever writes — getting PECS backwards causes an immediate, if sometimes confusing, compile error.
- Forgetting a class bound must come first in a multi-bound type parameter (`<T extends Number & Comparable<T>>`, never `<T extends Comparable<T> & Number>`).
- Reaching for a wildcard when a plain bounded type parameter would actually be simpler and clearer — wildcards are specifically for when the exact type parameter is genuinely unknown or irrelevant at the call site, not a default first choice.

---

## 📖 Further Reading

- Oracle Java Tutorials: "Bounded Type Parameters"
- Oracle Java Tutorials: "Wildcards" (including the Guidelines for Wildcard Use section, which formally covers PECS)

---

## 💡 Wisdom from Mímir

Whenever a wildcard choice feels ambiguous, stop and ask one question: am I only reading from this, or only writing to it? Reading-only → `extends`. Writing-only → `super`. That single question, applied mechanically, resolves the large majority of real-world wildcard decisions without needing to reason through the underlying type-safety argument from scratch every time.

---

## 🔗 Related Notes

- [[Generics and Type Parameters]]
- [[Comparable vs Comparator]]
- [[The Collections Framework Overview]]
- [[Java Codex]]
