---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Java
difficulty: Intermediate
tags:
  - modern-java
  - functional-interfaces
  - lambda-expressions
aliases:
  - FunctionalInterface Annotation
  - Predicate Function Supplier Consumer
publish: true
permalink: java/functional-interfaces
---

# <span class="rune">ᛟ</span> Functional Interfaces

> *A functional interface is nothing more than an interface with exactly one job — and that single constraint is the entire reason a lambda expression is able to stand in for it at all.*

---

## 🎯 Purpose

A **functional interface** is any interface with exactly one abstract method (it can still have any number of default or static methods) — this single-method constraint is precisely what makes it possible for a lambda expression to serve as a complete implementation. Java 8 shipped `java.util.function` with a rich, standardized set of common functional interfaces so most everyday needs don't require defining a custom one at all.

---

## 🧠 Key Ideas

- The `@FunctionalInterface` annotation is optional but strongly recommended — it makes the compiler enforce the single-abstract-method rule, catching an accidental second abstract method as a compile error rather than a confusing runtime surprise.
- `Predicate<T>` — takes a `T`, returns a `boolean` (a yes/no test): `.test(T)`.
- `Function<T, R>` — takes a `T`, returns an `R` (a transformation): `.apply(T)`.
- `Consumer<T>` — takes a `T`, returns nothing (an action performed on a value): `.accept(T)`.
- `Supplier<T>` — takes nothing, returns a `T` (a value producer): `.get()`.
- `BiFunction<T, U, R>`, `BiConsumer<T, U>`, `BinaryOperator<T>`, and others extend this same family to two-argument and same-type variants — covering the overwhelming majority of common shapes without needing a custom interface.

---

## ⚙️ How It Works

Because a functional interface has exactly one abstract method, providing a lambda expression unambiguously tells the compiler exactly which method that lambda implements — there's no possible ambiguity about "which method does this lambda's body correspond to," since only one exists. This single-method guarantee is what the `@FunctionalInterface` annotation formally checks: adding a second abstract method to an interface already marked `@FunctionalInterface` produces an immediate compile error, protecting the interface's contract against accidental changes that would break every lambda already written against it.

```text
@FunctionalInterface
interface Calculator {
    int calculate(int a, int b);   // exactly ONE abstract method — lambda-compatible
    // adding a second abstract method here would break @FunctionalInterface's guarantee
}

Calculator add = (a, b) -> a + b;      // lambda implements calculate()
Calculator multiply = (a, b) -> a * b;  // a DIFFERENT implementation of the SAME interface
```

---

## 💻 Examples

```java
// The four core java.util.function interfaces
Predicate<String> isEmpty = String::isEmpty;
System.out.println(isEmpty.test(""));         // true

Function<String, Integer> length = String::length;
System.out.println(length.apply("hello"));    // 5

Consumer<String> printer = System.out::println;
printer.accept("Hello, Consumer!");

Supplier<String> greeting = () -> "Hello, Supplier!";
System.out.println(greeting.get());

// Two-argument variants
BiFunction<Integer, Integer, Integer> add = (a, b) -> a + b;
System.out.println(add.apply(3, 4)); // 7

// Combining functional interfaces — default methods on the interfaces themselves
Predicate<String> isLong = s -> s.length() > 5;
Predicate<String> startsWithA = s -> s.startsWith("A");
Predicate<String> both = isLong.and(startsWithA);
System.out.println(both.test("Alexander")); // true — satisfies both conditions

Function<Integer, Integer> timesTwo = x -> x * 2;
Function<Integer, Integer> plusThree = x -> x + 3;
Function<Integer, Integer> combined = timesTwo.andThen(plusThree); // apply timesTwo, THEN plusThree
System.out.println(combined.apply(5)); // (5*2)+3 = 13

// A custom functional interface
@FunctionalInterface
interface TriFunction<A, B, C, R> {
    R apply(A a, B b, C c);
}

TriFunction<Integer, Integer, Integer, Integer> sum3 = (a, b, c) -> a + b + c;
System.out.println(sum3.apply(1, 2, 3)); // 6
```

---

## 🚀 Real World Applications

- Using `Predicate<T>` for filtering conditions throughout the [[The Streams API|Streams API]] (`.filter(predicate)`)
- Using `Function<T, R>` for transformation logic (`.map(function)`)
- Using `Consumer<T>` for side-effecting actions like logging or printing (`.forEach(consumer)`)
- Using `Supplier<T>` for lazy or on-demand value creation, such as default values computed only if actually needed
- Defining a custom functional interface when none of the standard `java.util.function` shapes fit a specific need (like the three-argument `TriFunction` example above)

---

## ⚖️ Advantages

- The standard `java.util.function` interfaces cover the overwhelming majority of common lambda shapes, meaning custom functional interfaces are rarely needed.
- `@FunctionalInterface` provides a compile-time safety net against accidentally breaking an interface's lambda-compatibility by adding a second abstract method later.
- Default methods on interfaces like `Predicate`/`Function` (`.and()`, `.or()`, `.andThen()`, `.compose()`) allow composing simple lambdas into more complex behavior without writing new classes.

---

## ⚠️ Limitations

- The generic naming convention (`Predicate`, `Function`, `Consumer`, `Supplier`) doesn't always map obviously to a specific business concept — sometimes a well-named custom functional interface communicates intent more clearly, even if functionally redundant with a standard one.
- Chaining many composed functional interfaces (`.andThen().andThen()...`) can become difficult to read and debug compared to a straightforward named method with clear intermediate steps.
- Overloading a method to accept several different functional interface types (e.g., both `Function<T,R>` and a custom interface with an identical shape) can create genuine ambiguity for the compiler when a lambda is passed, since it may match more than one candidate.

---

## 🚨 Common Mistakes

- Forgetting a functional interface must have **exactly** one abstract method — adding a second one (without `@FunctionalInterface` catching it early) causes any code using a lambda against that interface to fail to compile, often with a confusing error message.
- Reaching for a custom functional interface when an existing `java.util.function` type already matches the exact shape needed.
- Confusing `Function<T, R>`'s `.andThen()` (apply this function, then the next) with `.compose()` (apply the next function FIRST, then this one) — they chain in opposite directions.
- Not marking custom functional interfaces with `@FunctionalInterface`, missing the compile-time protection against accidentally breaking their single-method contract later.

---

## 📖 Further Reading

- Oracle documentation: `java.util.function` package overview
- Oracle Java Tutorials: "Lambda Expressions" (functional interface section)

---

## 💡 Wisdom from Mímir

Before defining a custom functional interface, check whether `java.util.function` already has exactly the shape you need — `Predicate`, `Function`, `Consumer`, `Supplier`, and their Bi- variants cover a surprising majority of real-world cases, and reaching for the standard ones keeps code more immediately recognizable to anyone else reading it.

---

## 🔗 Related Notes

- [[Lambda Expressions]]
- [[The Streams API]]
- [[Optional]]
- [[Java Codex]]
