---
type: concept
status: active
created: 2026-09-02
updated: 2026-09-02
technology: JavaScript
difficulty: Intermediate
tags:
  - async-await
  - promises
  - asynchronous
  - fundamentals
aliases:
  - Async/Await
  - async/await
  - await
  - Async Functions
---

# 📚 Async/Await

> *`async`/`await` is syntactic sugar over Promises that lets you write asynchronous code that reads top-to-bottom like synchronous code.*

---

## 🎯 Purpose

Promises fixed callback hell, but chaining `.then()` after `.then()` after `.then()` still doesn't *read* like normal code — you have to mentally track a chain of callbacks instead of a sequence of steps. `async`/`await` exists to close that gap: it lets you write asynchronous logic using ordinary control flow (`if`, `for`, `try`/`catch`, sequential statements) while the engine still handles everything asynchronously under the hood. It doesn't replace Promises — it's built directly on top of them.

---

## 🧠 Key Ideas

- The `async` keyword in front of a function declaration makes that function **always return a Promise**, even if you `return` a plain value inside it.
- `await` can only be used inside an `async` function (or, in modern environments, at the top level of a module). It pauses execution of that function until the Promise it's waiting on settles.
- While an `await` is paused, it does **not** block the rest of the program — only that specific `async` function's execution is paused; the JavaScript engine is free to run other code in the meantime.
- `await` on a Promise that **resolves** gives you back the resolved value directly — no `.then()` needed. `await` on a Promise that **rejects** throws an error you can catch with a normal `try`/`catch` block.
- Forgetting the `await` keyword is the single most common mistake — you get back the pending `Promise` object itself, not the value it will eventually hold.

---

## ⚙️ How It Works

Think of an `async` function as a recipe with pause points built in. Normally, JavaScript reads a function top to bottom without stopping. An `async` function reads the same way — except at every `await`, it says "I need to wait for this ingredient to finish before I can continue," steps aside, and lets the kitchen (the JavaScript engine) work on other recipes. The moment that ingredient is ready, the function picks up exactly where it left off, with the resolved value in hand.

Under the hood, this is just a Promise chain in disguise. The engine transforms your `async` function into something conceptually equivalent to a `.then()` chain — `await somePromise()` is roughly "attach the rest of this function as a `.then()` callback on `somePromise()`." You get the readability of synchronous-looking code without giving up any of the non-blocking behavior of Promises.

---

## 💻 Examples

```javascript
// A basic async function using fetch, written with await
async function getUser(id) {
  const response = await fetch(`https://api.example.com/users/${id}`);
  const user = await response.json();
  return user; // this value is automatically wrapped in a resolved Promise
}

getUser(42).then(user => console.log(user));
// getUser() itself returns a Promise, even though we wrote a normal `return`
```

```javascript
// try/catch replaces .catch() for error handling
async function getUserSafely(id) {
  try {
    const response = await fetch(`https://api.example.com/users/${id}`);

    if (!response.ok) {
      // fetch() does NOT reject on 404/500 — you have to check response.ok yourself
      throw new Error(`Request failed with status ${response.status}`);
    }

    const user = await response.json();
    return user;
  } catch (error) {
    // Catches network failures AND the manually thrown error above
    console.error("Failed to fetch user:", error.message);
    return null;
  }
}
```

```javascript
// The classic mistake: forgetting `await`
async function getUserBroken(id) {
  const user = fetch(`https://api.example.com/users/${id}`).then(r => r.json());
  console.log(user); // Promise { <pending> } — NOT the user object!
  return user;
}

async function getUserFixed(id) {
  const response = await fetch(`https://api.example.com/users/${id}`);
  const user = await response.json();
  console.log(user); // { id: 42, name: "Ada Lovelace", ... } — the actual data
  return user;
}
```

```javascript
// Running independent async operations in parallel instead of sequentially
async function getTwoUsersSlow(idA, idB) {
  const userA = await fetch(`/users/${idA}`).then(r => r.json()); // waits fully...
  const userB = await fetch(`/users/${idB}`).then(r => r.json()); // ...then starts this
  return [userA, userB]; // total time ≈ time(A) + time(B)
}

async function getTwoUsersFast(idA, idB) {
  // Both requests fire off immediately, THEN we wait for both together
  const [userA, userB] = await Promise.all([
    fetch(`/users/${idA}`).then(r => r.json()),
    fetch(`/users/${idB}`).then(r => r.json()),
  ]);
  return [userA, userB]; // total time ≈ max(time(A), time(B))
}
```

---

## 🚀 Real World Applications

- Fetching data from an API and using the result to render a UI, without nesting `.then()` callbacks.
- Sequencing dependent database calls (e.g. "look up the user, then look up their orders, then look up each order's items").
- Writing readable test code — most modern testing frameworks let test functions themselves be `async`.
- File I/O in Node.js using the `fs/promises` module, where each step (read config, parse it, write output) naturally follows the last.
- Coordinating multiple independent requests with `Promise.all`/`Promise.allSettled` while still keeping the surrounding code linear and readable.

---

## ⚖️ Advantages

- Reads like synchronous code, which is dramatically easier to follow than nested callbacks or long `.then()` chains.
- Error handling unifies around familiar `try`/`catch`, instead of a separate `.catch()` mental model.
- Debugging is easier — stepping through an `async` function in dev tools behaves much more like stepping through normal code, and stack traces are more meaningful.
- Composes cleanly with normal control flow: loops, conditionals, and early returns all just work.

---

## ⚠️ Limitations

- It's still asynchronous — `await` does not turn JavaScript into a multi-threaded language, and it doesn't make the underlying operation any faster.
- Awaiting things one after another when they don't depend on each other wastes time; you have to consciously reach for `Promise.all` to parallelize.
- An `async` function always returns a Promise, which can surprise developers expecting a plain value back from a normal-looking `return`.
- A single unhandled rejected `await` (no surrounding `try`/`catch`) crashes the `async` function's Promise into a silent rejection if nothing downstream is listening for it.

---

## 🚨 Common Mistakes

- Forgetting `await` and getting back a pending `Promise` object instead of the resolved value — then being confused when `console.log` shows `Promise { <pending> }`.
- Awaiting independent operations sequentially instead of using `Promise.all`, needlessly slowing things down.
- Forgetting that `fetch()` doesn't reject on HTTP error statuses (404, 500) — you must manually check `response.ok` and throw if needed.
- Wrapping an `await` in `try`/`catch` but forgetting that errors thrown *outside* the `try` block (e.g. in a `.then()` chained onto the result) won't be caught the same way.
- Using `await` inside a `forEach` loop expecting it to pause between iterations — `forEach` does not wait for async callbacks, so all iterations fire off immediately, in parallel and unordered. Use a `for...of` loop instead if sequential awaiting is needed.

---

## 📖 Further Reading

- MDN: "async function"
- MDN: "await"
- MDN: "Using Promises" (for the underlying model async/await sits on top of)

---

## 💡 Wisdom from Mímir

`async`/`await` is one of the rare cases in programming where the "easier to read" version and the "what's actually happening" version are the same thing, once you internalize that every `await` is just a pause point, not a stop sign for the rest of your program. The bugs that trip people up almost always come from forgetting that truth — either forgetting to `await` at all, or awaiting things sequentially that had no reason to wait for each other.

My rule of thumb: if two `await`s don't depend on each other's results, they don't belong on separate lines — they belong inside a `Promise.all`.

---

## 🔗 Related Notes

- [[Promises]] — the mechanism async/await is built entirely on top of
- [[The Event Loop - Call Stack, Task Queue, and Microtasks]] — explains exactly when a paused `await` resumes
- [[Callback Functions and Callback Hell]] — the nested-callback problem that Promises and async/await were designed to solve
- [[Error Handling with try/catch]] — the mechanism async/await reuses for asynchronous errors
- [[The Fetch API for HTTP Requests]] — the most common real-world use case for async/await
