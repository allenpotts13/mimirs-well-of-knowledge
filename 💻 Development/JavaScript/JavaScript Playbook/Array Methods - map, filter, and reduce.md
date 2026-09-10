---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: JavaScript
difficulty: Intermediate
tags:
  - arrays
  - array-methods
  - functional-programming
  - fundamentals
aliases:
  - map filter reduce
  - Array.map
  - Array.filter
  - Array.reduce
publish: true
---

# <span class="rune">ᛟ</span> Array Methods: map, filter, and reduce

> *`map` transforms, `filter` selects, `reduce` combines — and none of the three ever touches the array you started with.*

---

## 🎯 Purpose

Before ES5 popularized these methods, transforming an array meant hand-writing a `for` loop, an empty result array, and manual pushing — every single time, for every single transformation. `map`, `filter`, and `reduce` exist to replace that repetitive, error-prone pattern with three named, purpose-built operations that describe *what* you want done to a collection instead of *how* to loop over it manually.

---

## 🧠 Key Ideas

- **`map()`** transforms every element **1:1** into a new array of the **same length** — one input element in, one output element out, every time.
- **`filter()`** keeps only the elements that pass a test, producing a new array that's the **same length or shorter** than the original — it never adds or transforms elements, only selects.
- **`reduce()`** collapses the entire array down into a **single accumulated value** — and that value doesn't have to be a number; it can be a string, an array, an object, anything.
- All three return a **brand-new** array or value — none of them mutate the original array, unlike `.push()`, `.splice()`, or a manual imperative loop that reassigns elements in place.
- Each takes a **callback function** run once per element, and each callback receives `(element, index, array)` — most code only uses `element`, but the other two are there when needed.
- `reduce()` is the most general of the three — `map` and `filter` can technically both be implemented using `reduce`, though using the more specific method is almost always clearer.

---

## ⚙️ How It Works

Think of all three as walking down the array in order and handing each element to your callback function, one at a time — the difference is entirely in what they *do* with what the callback returns.

`map()` takes whatever your callback returns for each element and places it into a new array at the same position — so the output array is guaranteed to have exactly as many slots as the input, just potentially filled with different values (or different types entirely).

`filter()` expects your callback to return something truthy or falsy — a yes/no decision for each element. Elements whose callback returns truthy make it into the new array, unchanged; elements whose callback returns falsy are simply left out. Nothing is transformed, only kept or dropped.

`reduce()` is different in shape: your callback receives an **accumulator** (the running total-so-far) in addition to the current element, and whatever the callback returns becomes the accumulator going into the *next* element. By the time it reaches the end of the array, whatever the accumulator holds is the single final result. You give `reduce()` a starting value for the accumulator as its second argument, and that starting value can be anything — `0` for a sum, `[]` for building a new array, `{}` for building an object — which is exactly why `reduce()` can express so much more than "add up some numbers."

---

## 💻 Examples

```javascript
const prices = [10, 25, 40, 15];

// map — transform every element 1:1, same length out
const withTax = prices.map((price) => price * 1.08);
console.log(withTax); // [10.8, 27, 43.2, 16.2] — 4 elements in, 4 elements out

// filter — keep only elements matching a condition, possibly shorter
const affordable = prices.filter((price) => price < 30);
console.log(affordable); // [10, 25] — only the matching elements survive

// reduce — collapse everything down to a single value
const total = prices.reduce((sum, price) => sum + price, 0);
console.log(total); // 90 — one number out of four

// Original array is untouched by all three
console.log(prices); // [10, 25, 40, 15] — never mutated
```

```javascript
// reduce isn't just for numbers — the accumulator can be any shape
const words = ["the", "quick", "brown", "fox"];

// Building a single string
const sentence = words.reduce((acc, word) => acc + " " + word);
console.log(sentence); // "the quick brown fox" (no initial value — starts from words[0])

// Building an object — counting word lengths by first letter
const grouped = words.reduce((acc, word) => {
  const key = word[0];
  acc[key] = acc[key] || [];
  acc[key].push(word);
  return acc;
}, {});
console.log(grouped); // { t: ["the"], q: ["quick"], b: ["brown"], f: ["fox"] }
```

```javascript
// Chaining all three together — a very common real-world pattern
const orders = [
  { item: "Book", price: 15, paid: true },
  { item: "Pen", price: 3, paid: false },
  { item: "Laptop", price: 900, paid: true },
  { item: "Mug", price: 8, paid: true },
];

const totalPaid = orders
  .filter((order) => order.paid)              // keep only paid orders
  .map((order) => order.price)                  // pull out just the price
  .reduce((sum, price) => sum + price, 0);       // sum them up

console.log(totalPaid); // 923 — 15 + 900 + 8
```

```javascript
// A common mistake: expecting map to filter
const numbers = [1, 2, 3, 4, 5, 6];

// Wrong tool — map still returns 6 elements, with `undefined` for skipped ones
const wrong = numbers.map((n) => {
  if (n % 2 === 0) return n;
  // implicitly returns undefined for odd numbers
});
console.log(wrong); // [undefined, 2, undefined, 4, undefined, 6]

// Right tool — filter for selecting, map for transforming
const right = numbers.filter((n) => n % 2 === 0);
console.log(right); // [2, 4, 6]
```

---

## 🚀 Real World Applications

- Transforming API response data into the shape a UI component needs — e.g., mapping an array of `{ firstName, lastName }` objects into an array of full-name strings.
- Filtering a product list down to items matching a search query, a price range, or an in-stock flag before rendering.
- Calculating totals, averages, and summaries — cart totals, aggregate statistics, form validation counts — all classic `reduce()` use cases.
- Building lookup tables/dictionaries from an array of records with `reduce()`, turning `[{id, name}, ...]` into `{ id: name, ... }` for fast access by id.
- Chaining `filter().map().reduce()` together is an extremely common real-world pattern for "select relevant data, reshape it, then summarize it" pipelines.

---

## ⚖️ Advantages

- Declarative and self-documenting — `.filter(isActive).map(toDisplayName)` reads as *what* the code does, not the loop mechanics of *how*.
- Immutability by default (no mutation of the source array) makes code easier to reason about and avoids a class of bugs from accidentally shared array references.
- Chainable — since each returns a new array (except `reduce`, which returns the final value), these methods compose naturally into readable pipelines.
- `reduce()`'s generality means it can replace many custom loop patterns with one consistent, well-understood method.

---

## ⚠️ Limitations

- Each method call in a chain (`.filter().map().reduce()`) walks the entire array separately, which can be less performance-efficient than a single hand-written loop for very large datasets or hot code paths.
- `reduce()` in particular can produce dense, hard-to-read one-liners when overused for logic that would be clearer as an explicit loop or several named steps.
- None of the three short-circuit like `.some()` or `.find()` do — `map`/`filter`/`reduce` always process every element, even if you only need the first match.
- Deeply nested or overly clever `reduce()` calls (building complex objects in a single expression) can hurt readability more than they help, compared to a plain `for` loop with comments.

---

## 🚨 Common Mistakes

- Using `.map()` when you actually want to filter — it always returns the same length, so conditionally skipping elements produces `undefined` gaps instead of a shorter array (as in the example above).
- Using `.forEach()` (which returns `undefined`) when `.map()` was intended, then wondering why the "result" is `undefined`.
- Forgetting the initial value in `.reduce()` when the array might be empty — `[].reduce((a, b) => a + b)` throws a `TypeError` because there's no first element and no starting accumulator to fall back on.
- Mutating the accumulator object/array in `.reduce()` without returning it at the end of the callback, silently producing `undefined` on the next iteration.
- Assuming `.map()`, `.filter()`, or `.reduce()` mutate the original array — they never do; forgetting to capture the return value (`arr.map(...)` with no assignment) means the transformed data is simply discarded.

---

## 📖 Further Reading

- MDN: "Array.prototype.map()"
- MDN: "Array.prototype.filter()"
- MDN: "Array.prototype.reduce()"

---

## 💡 Wisdom from Mímir

When code reviewing, I use a simple litmus test on any array transformation: ask "what shape does the output need to be?" Same length, different values? `map`. Same or shorter, same values? `filter`. One single value of any shape? `reduce`. Picking the method that matches the *shape* of the answer, rather than reaching for whichever one you remember best, is what makes the resulting code read clearly to the next person.

`reduce()` is the one people either avoid entirely or overuse to the point of unreadability — there's rarely a middle ground. My rule: if you can't explain what the accumulator represents in one short phrase ("the running total," "the grouped-by-category object"), the `reduce()` call is probably trying to do too much in one step. Break it up.

---

## 🔗 Related Notes

- [[Higher-Order Functions]] — `map`, `filter`, and `reduce` are the canonical real-world examples of higher-order functions in everyday JavaScript
- [[Arrays vs Objects - When to Use Which]] — useful context for deciding what shape `reduce()` should build toward
- [[Arrow Functions vs Regular Functions]] — these methods are almost always paired with concise arrow function callbacks in modern code
