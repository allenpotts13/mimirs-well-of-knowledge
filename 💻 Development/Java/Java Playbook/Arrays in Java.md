---
type: concept
status: published
created: 2026-09-19
updated: 2026-09-19
technology: Java
difficulty: Beginner
tags:
  - java-fundamentals
  - arrays
  - data-structures
aliases:
  - Java Array Basics
  - Multidimensional Arrays
publish: true
permalink: java/arrays-in-java
---

# <span class="rune">ᛟ</span> Arrays in Java

> *An array's size is a promise made once, at creation, and never renegotiated — everything about how arrays behave in Java flows from that single fixed decision.*

---

## 🎯 Purpose

Arrays are Java's most basic fixed-size, indexed data structure — a single contiguous block of memory holding a fixed number of elements of the same type, accessed by a zero-based integer index. Unlike `ArrayList` and other Collections Framework types, an array's length is fixed permanently at creation time, which is both its main limitation and, for performance-sensitive code, part of its appeal.

---

## 🧠 Key Ideas

- Arrays are declared with `Type[] name` and created with `new Type[size]` — the size must be known (or computable) at creation time and can never change afterward.
- Array indices are zero-based: a 5-element array has valid indices `0` through `4`; accessing index `5` throws `ArrayIndexOutOfBoundsException`.
- Arrays are reference types — even an array of primitives is itself an object on the heap, and array variables hold a reference to it, following the same rules covered in [[Primitive Types vs Reference Types]].
- All elements in an array default to a type-appropriate zero value when created without explicit initialization: `0` for numeric types, `false` for `boolean`, `null` for reference types.
- Multidimensional arrays in Java are actually **arrays of arrays** — a `int[][]` is an array where each element is itself an `int[]`, and those inner arrays don't even need to be the same length (a "jagged" array).

---

## ⚙️ How It Works

When `new int[5]` executes, Java allocates a single contiguous block on the heap sized for exactly 5 integers, initialized to `0`, and returns a reference to it. Because the size is fixed at allocation, there's no way to "grow" an array in place — what looks like resizing (as `ArrayList` appears to do) is actually always creating an entirely new, larger array and copying the old elements into it, which is exactly what `ArrayList` does internally on your behalf, as covered in [[ArrayList vs LinkedList]].

```text
int[] nums = new int[3];        // [0, 0, 0] — allocated, zero-initialized
nums[0] = 10;
nums[1] = 20;
// nums is now [10, 20, 0]

int[][] grid = new int[2][3];   // an array of 2 elements, each itself an int[3]
grid[0][0] = 1;                 // first row, first column
```

---

## 💻 Examples

```java
// Declaration and creation
int[] scores = new int[5];              // all zeros initially
String[] names = {"Alice", "Bob", "Cara"}; // array literal, size inferred as 3

// Accessing and modifying
scores[0] = 95;
System.out.println(scores[0]);          // 95

// Iterating
for (int i = 0; i < scores.length; i++) {
    System.out.println(scores[i]);
}
for (int score : scores) {              // enhanced for-each — see [[Control Flow - if, switch, and Loops]]
    System.out.println(score);
}

// Out-of-bounds access throws at runtime, not compile time
// scores[10] = 1; // ArrayIndexOutOfBoundsException

// Multidimensional array
int[][] grid = {
    {1, 2, 3},
    {4, 5, 6}
};
System.out.println(grid[1][2]); // 6

// Jagged array — rows of different lengths
int[][] jagged = new int[3][];
jagged[0] = new int[]{1};
jagged[1] = new int[]{1, 2};
jagged[2] = new int[]{1, 2, 3};

// Useful java.util.Arrays helpers
int[] copy = Arrays.copyOf(scores, scores.length);
Arrays.sort(scores);
System.out.println(Arrays.toString(scores)); // proper printed representation
```

---

## 🚀 Real World Applications

- Representing a fixed-size grid or matrix (game boards, image pixel data, coordinate systems) with multidimensional arrays
- Using arrays as the backing storage for a known, unchanging quantity of data where the performance of direct indexed access matters
- Passing a fixed set of values to a method via varargs (`String... args`), which Java implements as an array under the hood
- Command-line argument handling via `public static void main(String[] args)`

---

## ⚖️ Advantages

- Fastest possible indexed access in Java — no wrapper overhead, no autoboxing (for primitive arrays), direct memory access by index.
- Fixed size means no hidden resizing cost or unpredictable memory reallocation during use.
- Multidimensional and jagged arrays model grids and irregular row-based data naturally.

---

## ⚠️ Limitations

- Fixed size means no way to add or remove elements — any "resize" requires manually creating a new array and copying, which is exactly what `ArrayList` automates.
- No built-in convenience methods on the array itself (no `.add()`, `.contains()`, `.sort()`) — everything requires the separate `java.util.Arrays` utility class.
- `Arrays.toString()` (or `deepToString()` for multidimensional arrays) must be used to print a readable representation — printing an array directly with `System.out.println(array)` prints its unhelpful memory-reference-based default representation instead.
- Printing an array directly, or comparing two arrays with `==`, exposes the fact that arrays are reference types — neither does what a beginner typically expects.

---

## 🚨 Common Mistakes

- Accessing an index equal to or greater than `array.length`, causing an `ArrayIndexOutOfBoundsException` at runtime — remember valid indices only go up to `length - 1`.
- Printing an array directly with `System.out.println(myArray)` and getting something like `[I@1b6d3586` instead of the actual contents — `Arrays.toString(myArray)` is required.
- Comparing two arrays with `==` expecting content comparison, when it actually compares references — `Arrays.equals(a, b)` compares contents.
- Assuming an array can be resized like an `ArrayList` — any apparent "growth" always means allocating an entirely new array and copying every element over.

---

## 📖 Further Reading

- Oracle Java Tutorials: "Arrays"
- `java.util.Arrays` class documentation (sort, copyOf, equals, toString, deepToString)

---

## 💡 Wisdom from Mímir

The moment `System.out.println(myArray)` prints something like `[I@1b6d3586` instead of readable content is the moment arrays' true nature as reference types becomes impossible to ignore. Reach for `Arrays.toString()` as a reflex, and remember that same reference-type reality is why `==` on two arrays almost never does what you'd expect.

---

## 🔗 Related Notes

- [[Primitive Types vs Reference Types]]
- [[ArrayList vs LinkedList]]
- [[Control Flow - if, switch, and Loops]]
- [[Java Codex]]
