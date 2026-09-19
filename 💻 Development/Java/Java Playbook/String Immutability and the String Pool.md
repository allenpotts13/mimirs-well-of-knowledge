---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Java
difficulty: Beginner
tags:
  - java-fundamentals
  - strings
  - immutability
aliases:
  - String Interning
  - Why Are Strings Immutable
publish: true
permalink: java/string-immutability-and-the-string-pool
---

# <span class="rune">ᛟ</span> String Immutability and the String Pool

> *Every "modification" you make to a String isn't actually a modification at all — it's a brand new String, and the old one is still sitting there, completely unchanged, until garbage collection eventually reclaims it.*

---

## 🎯 Purpose

`String` in Java is **immutable** — once created, its internal character data can never change. Every method that appears to modify a string (`concat()`, `replace()`, `toUpperCase()`, `substring()`) actually returns a brand-new `String` object, leaving the original untouched. This design choice, combined with the **string pool** (a cache of literal string values Java reuses automatically), explains a whole cluster of behaviors — from performance quirks to reference-equality surprises — that otherwise seem inconsistent.

---

## 🧠 Key Ideas

- Once a `String` object is created, none of its methods can change its actual character content — they all return a new `String` instead.
- String literals (`"hello"`) written directly in code are automatically stored in the **string pool**, a special cache — two identical literals refer to the exact same pooled object.
- Strings created with `new String("hello")` are **not** automatically pooled — even with identical content, they're a distinct object on the heap from the pooled literal.
- `String.intern()` manually forces a given string into the pool (or returns the existing pooled instance if already present), which is why it's the fix when reference-equality against pooled literals is genuinely needed.
- Immutability is what makes `String` safe to share freely across threads and safe to use as a `HashMap` key — its `hashCode()` can be computed once and cached forever, since the content can never change underneath it.

---

## ⚙️ How It Works

Because concatenation and other "modifying" methods can't actually change an existing `String`, each one allocates an entirely new `String` object holding the result and returns a reference to that new object — the original string, and any other reference still pointing to it, remains completely unaffected. The string pool exists specifically to reduce memory waste from this immutability: since string literals can never change, the JVM can safely let many identical literals throughout a program share one single underlying object rather than duplicating storage for the same text over and over.

```text
String a = "hello";        // "hello" placed in (or found in) the string pool
String b = "hello";        // b points to the SAME pooled object as a
String c = new String("hello"); // a NEW object on the heap, NOT the pooled one

a == b   // true  — same pooled object
a == c   // false — different objects, even though content is identical
a.equals(c) // true — equals() compares CONTENT, not reference
```

---

## 💻 Examples

```java
String greeting = "Hello";
greeting.concat(" World");           // returns a NEW string — greeting itself is unchanged!
System.out.println(greeting);        // still just "Hello"

String updated = greeting.concat(" World"); // must capture the return value
System.out.println(updated);         // "Hello World"

// String pool behavior
String a = "test";
String b = "test";
System.out.println(a == b);          // true — same pooled literal

String c = new String("test");
System.out.println(a == c);          // false — different objects
System.out.println(a.equals(c));     // true — same content

String d = c.intern();               // forces c's content into (or finds it in) the pool
System.out.println(a == d);          // true — now pointing to the same pooled object

// Building strings efficiently when many concatenations are needed
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 1000; i++) {
    sb.append(i);   // mutates the SAME StringBuilder — no new String per iteration
}
String result = sb.toString();
```

---

## 🚀 Real World Applications

- Understanding why a `String` method call that "does nothing" is actually just discarding its return value rather than modifying in place
- Using `.equals()` (never `==`) to compare string content anywhere user input, file data, or computed strings are involved
- Choosing `StringBuilder` over repeated `String` concatenation in a loop, as detailed further in [[StringBuilder vs String Concatenation]]
- Relying on `String`'s immutability to safely use it as a `HashMap`/`HashSet` key without fear of its hash code changing after insertion

---

## ⚖️ Advantages

- Immutability makes `String` inherently thread-safe — no synchronization is ever needed to share a string across threads.
- The string pool significantly reduces memory usage for programs with many repeated literal strings.
- A cached, unchanging hash code makes `String` an efficient and safe key type for hash-based collections.
- Immutable strings can be freely passed around and returned without any risk of a caller unexpectedly modifying shared data.

---

## ⚠️ Limitations

- Every "modifying" operation actually allocates a new object — repeated concatenation in a loop creates and discards many intermediate strings, which is genuinely wasteful at scale (see [[StringBuilder vs String Concatenation]]).
- `new String("literal")` deliberately bypasses the pool, and it's easy to write this by accident and then be confused by `==` behaving differently than expected.
- `String.intern()` has its own performance and permanent-memory-retention tradeoffs and shouldn't be reached for reflexively just to make `==` work.

---

## 🚨 Common Mistakes

- Calling a "modifying" method like `.trim()`, `.replace()`, or `.toUpperCase()` and forgetting to capture its return value, then being confused the original string appears unchanged.
- Using `==` to compare two strings for equality instead of `.equals()` — works accidentally for pooled literals, then fails mysteriously the moment one string comes from `new String(...)`, user input, or concatenation at runtime.
- Concatenating strings with `+` inside a loop with many iterations instead of using `StringBuilder`, causing significant unnecessary object allocation.
- Assuming two strings built from runtime concatenation (`"a" + variable`) are automatically pooled the same way compile-time literal concatenation is — only compile-time-constant expressions get pooled automatically.

---

## 📖 Further Reading

- Oracle Java Tutorials: "Strings"
- Oracle documentation: `String.intern()`

---

## 💡 Wisdom from Mímir

Any time `==` on two strings gives a surprising answer, the string pool is almost always the explanation — not a bug in Java, and not a bug in your code, just two different objects (or two accidentally-the-same pooled ones) being compared by reference instead of content. Make `.equals()` the automatic reflex for string comparison, and save `==` for the rare case where reference identity is genuinely what you mean.

---

## 🔗 Related Notes

- [[StringBuilder vs String Concatenation]]
- [[Object Equality - equals(), ==, and hashCode()]]
- [[Primitive Types vs Reference Types]]
- [[Java Codex]]
