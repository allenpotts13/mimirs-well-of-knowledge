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
  - performance
aliases:
  - StringBuilder vs +
  - String Concatenation Performance
publish: true
permalink: java/stringbuilder-vs-string-concatenation
---

# <span class="rune">ᛟ</span> StringBuilder vs String Concatenation

> *Every `+` on a String in a loop is secretly building, then throwing away, an intermediate String on every single pass — StringBuilder is what happens when you stop paying that tax.*

---

## 🎯 Purpose

Because [[String Immutability and the String Pool|Strings are immutable]], every concatenation with `+` creates an entirely new `String` object rather than modifying an existing one — fine for a handful of one-off concatenations, but genuinely wasteful when done repeatedly inside a loop. `StringBuilder` provides a **mutable** character sequence specifically designed for building up a string incrementally, without creating a new object on every single append.

---

## 🧠 Key Ideas

- `String + String` always creates a new `String` object — repeating this N times in a loop creates (and immediately discards) N intermediate objects.
- `StringBuilder` maintains one internal, resizable character buffer and mutates it in place — calling `.append()` repeatedly modifies the same object rather than creating a new one each time.
- The compiler automatically converts a simple chain of `+` concatenations written as a single expression (like `"a" + b + "c"`) into an equivalent `StringBuilder` sequence behind the scenes — the real cost only appears when concatenation happens **across multiple iterations of a loop**, which the compiler cannot optimize away.
- `.toString()` converts the finished `StringBuilder` back into an immutable `String` once building is complete.
- `StringBuffer` is `StringBuilder`'s older, synchronized (thread-safe) equivalent — `StringBuilder` is preferred whenever thread safety isn't specifically needed, which is the vast majority of the time.

---

## ⚙️ How It Works

A single-expression concatenation like `String result = "Score: " + score + " points";` is compiled by `javac` into roughly one `StringBuilder` sequence automatically — so writing that one line by hand offers no real advantage. The actual cost appears when concatenation happens *repeatedly across loop iterations*: `result = result + item;` inside a loop creates a brand-new `StringBuilder`, appends to it, converts it to a `String`, and discards the builder — every single iteration — because the compiler has no way to know the loop is really one long accumulation rather than N separate statements.

```text
// Compiler CAN optimize a single expression:
String s = "a" + b + "c";  
// → roughly: new StringBuilder().append("a").append(b).append("c").toString()

// Compiler CANNOT optimize across loop iterations:
String result = "";
for (...) {
    result = result + item;   // NEW StringBuilder + NEW String, EVERY iteration
}
```

---

## 💻 Examples

```java
// Inefficient — creates a new String object on every iteration
String result = "";
for (int i = 0; i < 1000; i++) {
    result = result + i + ", ";   // O(n²) overall — each concatenation copies everything so far
}

// Efficient — one mutable buffer, appended to in place
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 1000; i++) {
    sb.append(i).append(", ");    // O(n) overall — no repeated copying
}
String result2 = sb.toString();

// StringBuilder's other useful methods
StringBuilder sb2 = new StringBuilder("Hello World");
sb2.insert(5, ",");              // "Hello, World"
sb2.reverse();                   // reverses in place
sb2.deleteCharAt(0);              // removes a character
sb2.replace(0, 4, "XXXX");        // replaces a range

// A single-line concatenation is fine as-is — the compiler already optimizes it
String message = "Hello, " + name + "! You are " + age + " years old.";
```

---

## 🚀 Real World Applications

- Building a large string incrementally inside a loop (constructing a CSV row, building an HTML fragment, assembling a large log message)
- Reversing, inserting into, or deleting from a string in place without creating multiple intermediate immutable copies
- Any performance-sensitive code path that constructs strings from many small pieces, where allocation overhead genuinely matters
- Building SQL query strings, formatted reports, or serialized data piece by piece

---

## ⚖️ Advantages

- Dramatically reduces object allocation and garbage collection pressure compared to repeated concatenation in a loop.
- The mutable buffer supports efficient insertion, deletion, and reversal operations that would require creating multiple new `String` objects if done with immutable strings alone.
- `StringBuilder`'s internal buffer grows dynamically as needed, similar in spirit to how `ArrayList` manages its backing array.

---

## ⚠️ Limitations

- `StringBuilder` is not thread-safe — concurrent modification from multiple threads without external synchronization can corrupt its internal state; `StringBuffer` exists specifically for that scenario, at the cost of synchronization overhead.
- For a small, fixed number of concatenations (especially within a single expression), manually using `StringBuilder` offers no measurable benefit over plain `+`, since the compiler already optimizes that case.
- Using `StringBuilder` everywhere out of habit, even for trivial one-off concatenations, adds unnecessary verbosity without a real performance benefit.

---

## 🚨 Common Mistakes

- Concatenating strings with `+` inside a loop with many iterations, not realizing each pass silently creates and discards an intermediate object — a real and measurable performance problem at scale.
- Assuming `StringBuilder` is required even for simple, single-expression concatenation — the compiler already handles that case efficiently on its own.
- Using `StringBuilder` in genuinely multithreaded code where `StringBuffer` (or external synchronization) is actually needed for correctness.
- Forgetting to call `.toString()` at the end, and trying to use the `StringBuilder` object itself somewhere a `String` is expected (e.g., in a method that specifically requires `String`, not `CharSequence`).

---

## 📖 Further Reading

- Oracle documentation: `StringBuilder` and `StringBuffer` class references
- Oracle Java Tutorials: "Strings" (concatenation section)

---

## 💡 Wisdom from Mímir

The rule of thumb that actually matters: string concatenation inside a **loop** is where `StringBuilder` earns its keep. A single-line concatenation expression, even a long one, is already handled efficiently by the compiler — don't reach for `StringBuilder` there just because it "sounds more performant." Save the reach for where the loop actually is.

---

## 🔗 Related Notes

- [[String Immutability and the String Pool]]
- [[ArrayList vs LinkedList]]
- [[Java Codex]]
