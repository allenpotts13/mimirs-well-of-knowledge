---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Java
difficulty: Intermediate
tags:
  - oop
  - equality
  - hashcode
aliases:
  - equals and hashCode contract
  - Overriding equals
publish: true
permalink: java/object-equality-equals-and-hashcode
---

# <span class="rune">ᛟ</span> Object Equality - equals(), ==, and hashCode()

> *`==` asks "are these the exact same object in memory?" `equals()` asks whatever question you decide it should ask. Overriding one without the other is how a perfectly reasonable object quietly goes missing from a HashSet.*

---

## 🎯 Purpose

Java draws a hard line between reference equality (`==`, comparing whether two variables point to the *identical* object) and logical equality (`equals()`, comparing whether two objects should be *considered* equal, by whatever definition the class chooses). Getting this distinction right — and honoring the tightly linked contract between `equals()` and `hashCode()` — is essential the moment custom objects are used as `HashMap`/`HashSet` keys or compared for meaningful equality anywhere in a program.

---

## 🧠 Key Ideas

- `==` on reference types always compares memory references (are these literally the same object), never content — even for two objects with identical field values, `==` returns `false` unless they're the exact same instance.
- `.equals()` is a method, inherited from `Object`, whose *default* implementation is just `==` — unless a class overrides it, `.equals()` behaves identically to `==`.
- Overriding `equals()` to compare actual content requires **also** overriding `hashCode()` — the contract states that equal objects (per `.equals()`) **must** produce the same `hashCode()` value, or hash-based collections (`HashMap`, `HashSet`) will silently misbehave.
- `Integer` and other wrapper classes cache and reuse small values (typically -128 to 127) — meaning `==` on two small boxed `Integer` values can accidentally appear to work, then mysteriously stop working the moment the value exceeds that cached range.
- Records (Java 16+) automatically generate correct `equals()`, `hashCode()`, and `toString()` based on their fields, sidestepping this entire manual implementation burden for simple data-carrier classes.

---

## ⚙️ How It Works

`HashMap` and `HashSet` use `hashCode()` first to quickly narrow down which internal "bucket" an object belongs in, then use `equals()` to confirm an actual match among objects that landed in the same bucket. If two objects are `.equals()` to each other but return *different* `hashCode()` values, they can end up sorted into different buckets entirely — meaning a `HashSet` might contain what looks like a duplicate (by `equals()`), or a `HashMap` lookup for a key that's genuinely `.equals()` to a stored key can fail to find it, simply because the hash codes didn't match and the lookup never even checked the right bucket.

```text
class Point {
    int x, y;
    // If you override equals() to compare x and y...
    // ...you MUST also override hashCode() based on x and y,
    // or two "equal" Points might land in different HashMap buckets
    // and the map will never find one using the other as a lookup key.
}
```

---

## 💻 Examples

```java
public class Point {
    private final int x, y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;                 // same object — fast path
        if (!(obj instanceof Point)) return false;      // different type — can't be equal
        Point other = (Point) obj;
        return this.x == other.x && this.y == other.y;  // actual content comparison
    }

    @Override
    public int hashCode() {
        return Objects.hash(x, y);   // MUST be consistent with equals() above
    }
}

Point p1 = new Point(1, 2);
Point p2 = new Point(1, 2);

System.out.println(p1 == p2);        // false — different objects in memory
System.out.println(p1.equals(p2));   // true  — same content, thanks to the override

Set<Point> points = new HashSet<>();
points.add(p1);
System.out.println(points.contains(p2)); // true — ONLY because hashCode() is consistent with equals()

// The Integer caching trap
Integer a = 100;
Integer b = 100;
System.out.println(a == b);          // true — both within the cached range (-128 to 127)

Integer c = 200;
Integer d = 200;
System.out.println(c == d);          // false — outside the cache, two distinct objects!
System.out.println(c.equals(d));     // true — always correct, regardless of caching
```

---

## 🚀 Real World Applications

- Overriding `equals()`/`hashCode()` on any custom class that will be used as a `HashMap` key, stored in a `HashSet`, or compared for logical (not reference) equality anywhere
- Using `.equals()` (never `==`) to compare `String`, `Integer`, or any other object type for content equality
- Choosing Java records for simple immutable data classes specifically to get a correct `equals()`/`hashCode()` implementation generated automatically
- Debugging a "why isn't this object found in my HashSet even though an identical one is in it" bug by checking whether `equals()` and `hashCode()` are both properly overridden and consistent

---

## ⚖️ Advantages

- A correctly implemented `equals()`/`hashCode()` pair makes custom objects behave exactly as expected in every hash-based collection, with no special-case handling needed elsewhere.
- `Objects.equals()` and `Objects.hash()` (utility methods) make writing a correct, null-safe implementation of both methods straightforward and far less error-prone than doing it entirely by hand.
- Records eliminate this entire category of manual implementation for simple immutable value classes.

---

## ⚠️ Limitations

- The `equals()`/`hashCode()` contract is easy to violate accidentally (overriding one but not the other, or overriding them inconsistently), and violations often don't surface as an obvious error — just quietly wrong behavior in collections.
- `hashCode()` based on mutable fields is dangerous — if an object's hash-relevant fields change *after* it's been added to a `HashSet`/used as a `HashMap` key, it may become permanently "lost" (unfindable) in that collection.
- `Integer` caching behavior (and similar caching for other small wrapper values) is JVM-implementation detail that happens to be specified for a *guaranteed* range, but relying on `==` for boxed types at all is fragile regardless of the exact cached range.

---

## 🚨 Common Mistakes

- Using `==` to compare `String`, `Integer`, or other object types for content equality instead of `.equals()` — see also [[String Immutability and the String Pool]] for the string-specific version of this exact trap.
- Overriding `equals()` without also overriding `hashCode()` (or vice versa), breaking the contract and causing subtle, hard-to-diagnose bugs in `HashMap`/`HashSet` usage.
- Using a mutable field in `hashCode()`'s calculation, then mutating that field after the object has already been inserted into a hash-based collection — the object effectively becomes lost inside its own collection.
- Relying on `Integer`/`Long`/other wrapper caching behavior with `==` and being confused when it "works" for small values but breaks for larger ones.

---

## 📖 Further Reading

- Oracle documentation: `Object.equals()` and `Object.hashCode()` (the full contract, stated explicitly in the Javadoc)
- Oracle documentation: `java.util.Objects` (equals, hash, hashCode utility methods)
- JEP 395: "Records"

---

## 💡 Wisdom from Mímir

The `equals()`/`hashCode()` contract is one of the few places in Java where breaking a rule doesn't throw an exception — it just quietly produces wrong answers from collections that look like they should work. Whenever you override one, override the other in the same commit, every time, as a non-negotiable pair.

---

## 🔗 Related Notes

- [[String Immutability and the String Pool]]
- [[HashMap vs TreeMap vs LinkedHashMap]]
- [[HashSet vs TreeSet vs LinkedHashSet]]
- [[Java Codex]]
