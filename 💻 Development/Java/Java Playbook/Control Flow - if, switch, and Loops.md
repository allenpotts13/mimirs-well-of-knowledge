---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Java
difficulty: Beginner
tags:
  - java-fundamentals
  - control-flow
  - switch-expressions
aliases:
  - Switch Expressions
  - Enhanced Switch
publish: true
permalink: java/control-flow-if-switch-and-loops
---

# <span class="rune">ᛟ</span> Control Flow - if, switch, and Loops

> *Modern Java's switch expression fixed the single most infamous footgun in the classic language — fall-through — by making the safe behavior the only behavior, rather than something you have to remember to opt into.*

---

## 🎯 Purpose

Java's control flow constructs — `if`/`else`, the classic and modern `switch`, and the three loop forms (`for`, `while`, `do-while`, plus the enhanced for-each) — are largely familiar from other C-family languages, but Java's `switch` statement has a long-standing fall-through trap that the newer **switch expression** (Java 14+) was specifically designed to eliminate. Knowing both forms — and why the newer one exists — matters for reading both legacy and modern Java code.

---

## 🧠 Key Ideas

- Classic `switch` **statements** fall through to the next `case` unless an explicit `break` is used — a missing `break` silently executes every subsequent case's code too.
- Modern `switch` **expressions** (using `->` instead of `:`) don't fall through at all, can directly return a value, and require exhaustiveness (every possible case, including a `default`, must be covered when used as an expression).
- The enhanced for-each loop (`for (Type item : collection)`) is the preferred way to iterate when the index itself isn't needed — it's shorter and eliminates off-by-one indexing bugs entirely.
- `do-while` guarantees its body runs **at least once**, since the condition is checked *after* the first iteration — different from `while`, which checks before ever running the body.
- `break`/`continue` work identically across all loop types, and labeled versions (`outer:` before a loop, then `break outer;`) let you break out of a specific outer loop from within a nested one.

---

## ⚙️ How It Works

The classic `switch` statement was designed around C's fall-through semantics — after matching a case, execution continues into the *next* case's code unless a `break` explicitly stops it, which is useful for intentionally grouping multiple cases together but dangerous when forgotten. The switch expression introduced in Java 14 flips the default: each arm (`case X ->`) is scoped to just that case with no fall-through possible, and because it's an *expression* (it produces a value directly, usable in an assignment), the compiler also enforces that every possible input is handled — catching a missing case at compile time instead of letting it silently do nothing at runtime.

```text
// Classic switch — fall-through unless you break
switch (day) {
    case MONDAY:
    case TUESDAY:
        System.out.println("Early week");
        break;          // WITHOUT this, execution falls into the next case
    case WEDNESDAY:
        System.out.println("Midweek");
        break;
}

// Switch expression — no fall-through possible, returns a value directly
String result = switch (day) {
    case MONDAY, TUESDAY -> "Early week";
    case WEDNESDAY -> "Midweek";
    default -> "Other";
};
```

---

## 💻 Examples

```java
// if/else chain
int score = 85;
if (score >= 90) {
    System.out.println("A");
} else if (score >= 80) {
    System.out.println("B");
} else {
    System.out.println("C or below");
}

// Classic switch statement — fall-through risk
int month = 2;
switch (month) {
    case 12:
    case 1:
    case 2:
        System.out.println("Winter");
        break;
    default:
        System.out.println("Not winter");
}

// Modern switch expression — safer, returns a value
String season = switch (month) {
    case 12, 1, 2 -> "Winter";
    case 3, 4, 5 -> "Spring";
    default -> "Other";
};

// Loops
for (int i = 0; i < 5; i++) { System.out.println(i); }

for (String name : List.of("Alice", "Bob")) { System.out.println(name); } // enhanced for-each

int attempts = 0;
do {
    attempts++;
} while (attempts < 3); // runs at least once, even if the condition starts false

// Labeled break out of a nested loop
outer:
for (int i = 0; i < 3; i++) {
    for (int j = 0; j < 3; j++) {
        if (j == 1) break outer;
    }
}
```

---

## 🚀 Real World Applications

- Using a switch expression to map an enum value directly to a computed result in one concise, exhaustive block
- Choosing the enhanced for-each loop when iterating a `List`/`Set`/array purely to read each element, avoiding manual index management
- Using `do-while` for input-validation loops that must attempt at least one read before checking whether to continue
- Using a labeled `break` to escape a nested loop early during a matrix or grid search, rather than tracking a separate boolean flag

---

## ⚖️ Advantages

- Switch expressions eliminate an entire historical category of fall-through bugs by making the safe behavior the default, not an opt-in.
- Compiler-enforced exhaustiveness on switch expressions over enums catches a missing case immediately, at compile time, rather than as a silent runtime gap.
- The enhanced for-each loop is both more concise and inherently safer than manual indexing for simple iteration.

---

## ⚠️ Limitations

- Switch expressions can't easily express complex multi-statement logic per case as cleanly as a classic switch statement can (though a block form with `yield` exists for that).
- Legacy Java code (and most textbooks written before Java 14) still uses the classic fall-through `switch`, so both forms need to be recognizable even if only the modern one is preferred for new code.
- The enhanced for-each loop doesn't expose the current index, requiring a classic indexed `for` loop instead whenever the position itself is needed.

---

## 🚨 Common Mistakes

- Forgetting `break` in a classic `switch` statement, causing execution to silently fall through into unrelated subsequent cases.
- Using a classic `switch` when a `switch` expression would both be safer and more concise, out of habit or unfamiliarity with the newer syntax.
- Trying to modify a collection while iterating it with an enhanced for-each loop, which throws a `ConcurrentModificationException` — an explicit `Iterator` (see [[Iterators and the Iterable Interface]]) is required for safe removal during iteration.
- Confusing `while` and `do-while` — reaching for `do-while` without actually needing the "runs at least once" guarantee, or vice versa.

---

## 📖 Further Reading

- Oracle Java Tutorials: "The switch Statement"
- JEP 361: "Switch Expressions" (Java 14)

---

## 💡 Wisdom from Mímir

When reading unfamiliar Java code, a classic `switch` without a `break` at the end of every case isn't automatically a bug — but it's always worth a second look. And when writing new code, reach for the switch expression by default; the fall-through behavior it removed caused more real production bugs than almost any other single language quirk in Java's history.

---

## 🔗 Related Notes

- [[Variables, Operators, and Type Casting]]
- [[Enums in Java]]
- [[Iterators and the Iterable Interface]]
- [[Java Codex]]
