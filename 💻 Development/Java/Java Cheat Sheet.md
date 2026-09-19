---
publish: true
status: published
permalink: java/java-cheat-sheet
---

# <span class="rune">ᛊ</span> Java Cheat Sheet

> Dense reference across core Java. Each section links back to the full [[Java Codex|concept note]] for depth.

---

## Primitives, Casting, and Operators

```java
byte, short, int, long, float, double, char, boolean   // the 8 primitives

int i = 100;
double d = i;          // widening — automatic
int back = (int) d;    // narrowing — MUST cast explicitly, truncates (does not round)

int result = 7 / 2;       // 3 — integer division truncates
double correct = 7 / 2.0; // 3.5 — one operand must be floating-point

byte count = 10;
count += 5;   // compiles — compound assignment includes an implicit cast
```

See: [[Primitive Types vs Reference Types]] · [[Variables, Operators, and Type Casting]]

---

## Control Flow

```java
// Modern switch expression — no fall-through, can return a value
String season = switch (month) {
    case 12, 1, 2 -> "Winter";
    case 3, 4, 5 -> "Spring";
    default -> "Other";
};

for (int i = 0; i < 5; i++) { }
for (String s : list) { }              // enhanced for-each — no index access
while (condition) { }
do { } while (condition);               // runs body at least once

outer:
for (int i = 0; i < 3; i++) {
    for (int j = 0; j < 3; j++) {
        if (j == 1) break outer;        // labeled break
    }
}
```

See: [[Control Flow - if, switch, and Loops]]

---

## Strings

```java
String s = "hello";
s.concat(" world");        // returns a NEW String — s itself is unchanged
s = s.concat(" world");    // must capture the return value

"a" == "a"                 // true — same pooled literal
new String("a") == "a"     // false — different object
str1.equals(str2)           // ALWAYS use for content comparison, never ==

// Efficient building — use inside loops, never repeated + concatenation
StringBuilder sb = new StringBuilder();
sb.append("a").append(1).append(true);
String result = sb.toString();
```

See: [[String Immutability and the String Pool]] · [[StringBuilder vs String Concatenation]]

---

## Classes, Objects, Access

```java
public class Car {
    private String make;             // private — this class only
    protected int year;              // package + subclasses (via inheritance)
    String model;                    // package-private (default — NOT public!)
    public String vin;               // anywhere

    static int totalCars = 0;         // ONE copy, shared by the class
    int id;                           // separate copy PER instance

    public Car(String make) {
        this.make = make;             // "this." disambiguates field from parameter
        totalCars++;
    }
}
```

| Modifier | Access |
|---|---|
| `private` | this class only |
| *(none)* | package-private — same package only |
| `protected` | package + subclasses anywhere |
| `public` | anywhere |

See: [[Classes and Objects]] · [[Static vs Instance Members]] · [[Access Modifiers]]

---

## Constructors & Overloading

```java
class Rectangle {
    double width, height;

    Rectangle() { this(1, 1); }              // this(...) — MUST be first line, chains to below
    Rectangle(double side) { this(side, side); }
    Rectangle(double width, double height) {
        this.width = width;
        this.height = height;
    }
}
```

Overloading = same name, different parameter list. Return type ALONE is never enough to overload.

See: [[Constructors and Overloading]]

---

## Inheritance & Polymorphism

```java
class Animal {
    void speak() { System.out.println("..."); }
}

class Dog extends Animal {              // single inheritance only — one extends max
    Dog(String name) {
        super(name);                     // MUST be first line of constructor
    }

    @Override                            // ALWAYS use this — catches typos at compile time
    void speak() {
        super.speak();                    // calls Animal's original version too
        System.out.println("Woof!");
    }
}

Animal a = new Dog();
a.speak();   // calls Dog's version — decided at RUNTIME based on actual object type
```

See: [[Inheritance and the extends Keyword]] · [[Polymorphism - Overriding vs Overloading]] · [[The this and super Keywords]]

---

## Abstract Classes vs Interfaces

```java
abstract class Shape {                   // can hold state + partial implementation
    String color;
    abstract double area();               // subclasses MUST implement
    void describe() { System.out.println(color); } // shared, concrete
}

interface Drawable {                      // pure capability — no instance fields
    void draw();
    default void printLabel() { System.out.println("Drawable"); } // Java 8+ default method
}

class Circle extends Shape implements Drawable, Comparable<Circle> {
    // extend ONE class, implement MANY interfaces
}
```

**"Is-a" with shared state** → abstract class. **"Can-do" capability** → interface.

See: [[Abstract Classes vs Interfaces]] · [[Enums in Java]]

---

## Equality & hashCode

```java
@Override
public boolean equals(Object obj) {
    if (this == obj) return true;
    if (!(obj instanceof Point)) return false;
    Point other = (Point) obj;
    return this.x == other.x && this.y == other.y;
}

@Override
public int hashCode() {
    return Objects.hash(x, y);   // MUST stay consistent with equals()
}
```

**Rule**: override one, override BOTH — or HashMap/HashSet silently misbehave.
`Integer a = 100; Integer b = 100; a == b` → `true` (cached, -128 to 127). `a = 200; b = 200; a == b` → `false`.

See: [[Object Equality - equals(), ==, and hashCode()]] · [[The Object Class and Its Methods]]

---

## Collections Framework

```java
List<String> list = new ArrayList<>();     // ordered, duplicates allowed, indexed
Set<String> set = new HashSet<>();         // no duplicates
Map<String, Integer> map = new HashMap<>(); // key-value, NOT part of Collection hierarchy
```

| Need | Choice |
|---|---|
| Fast indexed access | `ArrayList` |
| Frequent insert/remove at both ends | `LinkedList` / `ArrayDeque` |
| No order guarantee, fastest lookup | `HashMap` / `HashSet` |
| Insertion order preserved | `LinkedHashMap` / `LinkedHashSet` |
| Sorted order + range queries | `TreeMap` / `TreeSet` |

```java
map.put("a", 1);
map.put("a", 2);          // OVERWRITES — no duplicate keys, no error
map.getOrDefault("z", 0); // safe lookup with a fallback

treeSet.first(); treeSet.last(); treeSet.higher(x); treeSet.floor(x);
```

See: [[The Collections Framework Overview]] · [[ArrayList vs LinkedList]] · [[HashMap vs TreeMap vs LinkedHashMap]] · [[HashSet vs TreeSet vs LinkedHashSet]]

---

## Iterators & Safe Removal

```java
// UNSAFE — throws ConcurrentModificationException
// for (String s : list) { if (s.equals("x")) list.remove(s); }

Iterator<String> it = list.iterator();
while (it.hasNext()) {
    if (it.next().equals("x")) it.remove();  // SAFE — via the iterator itself
}

list.removeIf(s -> s.equals("x"));  // modern, cleaner alternative
```

See: [[Iterators and the Iterable Interface]]

---

## Comparable & Comparator

```java
class Person implements Comparable<Person> {
    public int compareTo(Person other) { return Integer.compare(this.age, other.age); }
}
Collections.sort(people);   // uses natural order (compareTo)

people.sort(Comparator.comparing(p -> p.name));                 // external, no class changes needed
people.sort(Comparator.comparingInt((Person p) -> p.age)
    .reversed()
    .thenComparing(p -> p.name));                                 // composable, multi-field sort
```

**Comparable** = one natural order, inside the class. **Comparator** = unlimited external orders.

See: [[Comparable vs Comparator]]

---

## Exceptions

```java
try {
    riskyOp();
} catch (FileNotFoundException e) {         // MOST specific first
    // ...
} catch (IOException e) {                    // broader — after
    // ...
} catch (IOException | SQLException e) {     // multi-catch — identical handling
    // ...
} finally {
    // ALWAYS runs — even on return, even on a new exception in catch
}

try (BufferedReader br = new BufferedReader(new FileReader("f.txt"))) {
    // br.close() called AUTOMATICALLY, guaranteed
} catch (IOException e) { }
```

| Type | Extends | Compiler enforces? |
|---|---|---|
| Checked | `Exception` | YES — catch or `throws` |
| Unchecked | `RuntimeException` | no |
| Error | `Error` | no — usually unrecoverable |

```java
class InsufficientFundsException extends Exception {          // checked
    public InsufficientFundsException(String msg, Throwable cause) {
        super(msg, cause);   // ALWAYS chain the original cause when wrapping
    }
}
```

See: [[Checked vs Unchecked Exceptions]] · [[Try-Catch-Finally and Try-With-Resources]] · [[Creating Custom Exceptions]]

---

## Generics

```java
class Box<T> {
    private T value;
    public void set(T value) { this.value = value; }
    public T get() { return value; }
}

public static <T extends Number> double sum(List<T> list) { }   // bounded — allows .doubleValue()

List<? extends Number> producer;   // read-only — safe to GET, not to add
List<? super Integer> consumer;    // write-only — safe to ADD, get returns only Object
```

**PECS**: Producer `extends`, Consumer `super`.

See: [[Generics and Type Parameters]] · [[Bounded Type Parameters and Wildcards]]

---

## Lambdas, Functional Interfaces, Streams

```java
Runnable r = () -> System.out.println("run");
Comparator<String> byLen = (a, b) -> a.length() - b.length();

Predicate<String> isEmpty = String::isEmpty;   // .test(T) -> boolean
Function<String, Integer> len = String::length; // .apply(T) -> R
Consumer<String> print = System.out::println;   // .accept(T) -> void
Supplier<String> greet = () -> "hi";             // .get() -> T
```

```java
List<String> result = names.stream()
    .filter(n -> n.length() > 3)      // intermediate — LAZY
    .map(String::toUpperCase)         // intermediate — LAZY
    .sorted()
    .collect(Collectors.toList());     // TERMINAL — pipeline actually runs HERE

Map<Integer, List<String>> byLen = names.stream()
    .collect(Collectors.groupingBy(String::length));

// A stream can only be consumed ONCE — reuse throws IllegalStateException
```

See: [[Lambda Expressions]] · [[Functional Interfaces]] · [[The Streams API]]

---

## Optional

```java
Optional<User> findUser(String id) {
    return Optional.ofNullable(database.lookup(id));  // safe — null becomes empty
}

String name = findUser("x").map(User::getName).orElse("Unknown");
findUser("x").ifPresent(u -> System.out.println(u.getName()));
findUser("x").orElseThrow(() -> new IllegalStateException("not found"));

// AVOID: .get() without checking first — throws NoSuchElementException if empty
// AVOID: using Optional as a field type or method parameter — return type only
```

See: [[Optional]]

---

## JVM: Memory & GC

```text
STACK  — per-thread, one frame per method call, holds locals & primitives, auto-reclaimed on return
HEAP   — shared, holds every object created with `new`, reclaimed by GC when unreachable

StackOverflowError  → unbounded/broken recursion, no base case
OutOfMemoryError    → heap fills faster than GC can reclaim (often a static collection that never shrinks)
```

GC reclaims by **reachability**, not usage — circular references with no path to a GC root ARE collected correctly.

See: [[Memory Model - Stack vs Heap]] · [[Garbage Collection Basics]] · [[How the JVM Works - Compilation, Bytecode, and Class Loading]]

---

## Concurrency

```java
new Thread(() -> System.out.println("run")).start();   // .start() — NEW thread
// .run() directly — WRONG, just a normal synchronous call, no concurrency

Thread worker = new Thread(task);
worker.start();
worker.join();   // caller WAITS for worker to finish

// Race condition — count++ is 3 steps (read, add, write), not atomic
synchronized void increment() { count++; }        // only one thread at a time

ExecutorService pool = Executors.newFixedThreadPool(4);
Future<Integer> f = pool.submit(() -> 42);
Integer result = f.get();       // blocks until done
pool.shutdown();                 // NEVER forget this — pool threads keep JVM alive otherwise
```

See: [[Threads and the Runnable Interface]] · [[Synchronization and Race Conditions]] · [[The Executor Framework]]

---

## 🔗 Related Notes

- [[Java Codex]]
- [[JavaScript Codex]]
