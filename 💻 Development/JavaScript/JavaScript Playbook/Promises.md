---
type: concept
status: active
created: 2026-09-02
updated: 2026-09-02
technology: JavaScript
difficulty: Intermediate
tags:
  - promises
  - asynchronous
  - fundamentals
  - es6
aliases:
  - Promise
  - JavaScript Promises
  - Promise.all
---

# 📚 Promises

> *A promise is a placeholder for a value that doesn't exist yet — a receipt for work that's still happening, which will eventually resolve to either a result or a reason it failed.*

---

## 🎯 Purpose

Asynchronous work — network requests, file reads, timers — used to be coordinated almost entirely through nested callbacks, which became unreadable and hard to reason about once more than a couple of async steps depended on each other (see [[Callback Functions and Callback Hell]]). Promises exist to give asynchronous operations a standard, composable object to represent "this will finish eventually," so that chaining, error handling, and coordinating multiple async operations can all follow one consistent, predictable API instead of ad-hoc nested functions.

---

## 🧠 Key Ideas

- A promise exists in exactly one of **three states**: **pending** (still working), **fulfilled** (succeeded, with a value), or **rejected** (failed, with a reason).
- A promise can only **settle once** — once it becomes fulfilled or rejected, it's locked into that outcome and value forever; it can never change state or "resolve again."
- **`.then()`** attaches a handler for fulfillment (and optionally rejection as a second argument), **`.catch()`** attaches a handler specifically for rejection, and **`.finally()`** runs regardless of outcome — useful for cleanup that should happen either way.
- **`.then()` always returns a brand-new promise**, no matter what — this is the mechanic that makes chaining (`.then().then().then()`) work: each `.then()` hands its result to the next one in line.
- **`Promise.all()`** waits for every promise in a collection to fulfill, but **fails fast** — if any single one rejects, the whole thing immediately rejects with that reason, discarding the rest.
- **`Promise.allSettled()`** waits for every promise regardless of outcome, and gives back a full report of which succeeded and which failed — nothing is discarded.
- **`Promise.race()`** settles as soon as the *first* promise in the collection settles, whether that one fulfills or rejects — the rest keep running but their outcomes are ignored.

---

## ⚙️ How It Works

Picture a promise as a sealed envelope handed to you the moment you kick off some async work — you don't know what's inside yet, but you're guaranteed it will eventually be opened exactly once, revealing either a success value or a failure reason. `.then()` is how you register "when this envelope opens with a success, do this with what's inside." `.catch()` is the same idea for failure. Because the envelope can only be opened once, and only ever holds one final outcome, you never have to worry about a handler firing twice or a promise "changing its mind" after settling.

The reason chaining works is that `.then()` doesn't just run your callback — it wraps whatever your callback returns in a *new* promise and hands that back to you. If your callback returns a plain value, the new promise immediately fulfills with it. If your callback returns *another* promise (like starting a second async operation), the new promise waits for that one to settle before adopting its outcome. This is why `fetchUser().then(user => fetchOrders(user.id)).then(orders => ...)` works as a clean, flat chain instead of nested callbacks — each `.then()` produces the next link automatically.

`Promise.all()`, `Promise.allSettled()`, and `Promise.race()` are three different strategies for coordinating *multiple* promises running at once, and the difference between them comes down to how much they tolerate failure and how long they're willing to wait. `Promise.all()` assumes you need *everything* to succeed for the result to be useful, so it stops the instant anything fails. `Promise.allSettled()` assumes partial results are still valuable, so it never gives up early — it just reports the full truth once everything is done. `Promise.race()` doesn't care about "everything" at all — it only cares about whoever crosses the finish line first, success or failure.

---

## 💻 Examples

```javascript
// Creating and consuming a basic promise
function delay(ms) {
  return new Promise((resolve, reject) => {
    if (ms < 0) {
      reject(new Error("Delay must be non-negative"));
      return;
    }
    setTimeout(() => resolve(`Waited ${ms}ms`), ms);
  });
}

delay(100)
  .then((result) => console.log(result))   // "Waited 100ms"
  .catch((error) => console.error(error))   // only runs if delay() rejects
  .finally(() => console.log("Done, either way"));
```

```javascript
// Chaining — each .then() returns a new promise, enabling a flat pipeline
function fetchUser(id) {
  return Promise.resolve({ id, name: "Ada" }); // simulating an async lookup
}
function fetchOrders(userId) {
  return Promise.resolve([{ id: 1, total: 25 }, { id: 2, total: 40 }]);
}

fetchUser(1)
  .then((user) => fetchOrders(user.id))          // returns a promise — chain waits for it
  .then((orders) => orders.reduce((sum, o) => sum + o.total, 0))
  .then((total) => console.log(`Order total: $${total}`)) // "Order total: $65"
  .catch((error) => console.error("Something failed:", error));
```

```javascript
// A promise settles exactly once — extra resolve/reject calls are silently ignored
const single = new Promise((resolve, reject) => {
  resolve("first");
  resolve("second"); // ignored — already fulfilled
  reject(new Error("too late")); // also ignored
});
single.then((value) => console.log(value)); // "first" — only ever "first"
```

```javascript
// Promise.all — fails fast on the first rejection
const taskA = Promise.resolve("A done");
const taskB = Promise.reject(new Error("B failed"));
const taskC = Promise.resolve("C done");

Promise.all([taskA, taskB, taskC])
  .then((results) => console.log(results)) // never runs
  .catch((error) => console.error(error.message)); // "B failed" — the whole thing rejects
```

```javascript
// Promise.allSettled — waits for everything, reports the full picture
Promise.allSettled([taskA, taskB, taskC]).then((results) => {
  console.log(results);
  // [
  //   { status: "fulfilled", value: "A done" },
  //   { status: "rejected", reason: Error: B failed },
  //   { status: "fulfilled", value: "C done" }
  // ]
});
```

```javascript
// Promise.race — settles as soon as the FIRST promise settles
const fast = new Promise((resolve) => setTimeout(() => resolve("fast wins"), 50));
const slow = new Promise((resolve) => setTimeout(() => resolve("slow wins"), 500));

Promise.race([fast, slow]).then((result) => console.log(result)); // "fast wins"
// `slow` keeps running in the background, but its outcome is simply ignored
```

---

## 🚀 Real World Applications

- Every modern HTTP client (`fetch`, Axios) returns promises to represent an in-flight network request.
- `Promise.all()` is standard for loading several independent pieces of data a page needs before rendering (e.g., user profile + settings + notifications, all fetched in parallel).
- `Promise.allSettled()` is used when partial success is acceptable — e.g. sending notifications to five services where one failing shouldn't stop the other four from completing.
- `Promise.race()` is commonly used to implement request timeouts — racing a real request against a promise that rejects after N seconds.
- Database drivers, file system APIs (Node's `fs.promises`), and most modern async libraries expose promise-based APIs as their primary interface, often layered with `async`/`await` on top (see [[Async/Await]]).

---

## ⚖️ Advantages

- Replaces deeply nested callbacks with flat, readable chains, directly solving the "callback hell" problem (see [[Callback Functions and Callback Hell]]).
- Provides a single, standard error-handling path (`.catch()`) instead of every async API inventing its own error-first-callback convention.
- Composable by design — `Promise.all`/`allSettled`/`race` let you coordinate many async operations using built-in, well-tested logic instead of hand-rolled counters and flags.
- Forms the direct foundation for `async`/`await`, which is really just cleaner syntax sitting on top of the exact same promise mechanics.

---

## ⚠️ Limitations

- Promises execute eagerly — the async work starts the moment the promise is created, not when `.then()` is attached, which can be surprising if you expected lazy evaluation.
- Error handling is easy to get subtly wrong — forgetting a `.catch()` anywhere in a chain lets rejections disappear silently or surface as unhandled rejection warnings.
- Chains that branch or need conditional logic can still get awkward to read compared to `async`/`await`, which is why the latter is now generally preferred for sequential async code.
- `Promise.all()`'s fail-fast behavior can be the *wrong* choice when you actually wanted partial results — reaching for `Promise.allSettled()` instead requires recognizing that need up front.

---

## 🚨 Common Mistakes

- Forgetting to `return` a promise inside a `.then()` callback, breaking the chain — the next `.then()` fires immediately with `undefined` instead of waiting for the nested async work.
- Omitting a `.catch()` at the end of a chain and being surprised when a rejection is silently swallowed or logged as an "unhandled promise rejection" with no clear source.
- Using `Promise.all()` when partial failure should be tolerated, causing one failed item to discard results from everything else that actually succeeded.
- Nesting `.then()` calls inside other `.then()` calls instead of chaining them flat — this recreates the exact "pyramid" problem promises were meant to solve.
- Assuming a promise can be "reused" or resolved a second time with a new value — once settled, it's permanently locked to that outcome.

---

## 📖 Further Reading

- MDN: "Using promises"
- MDN: "Promise"
- MDN: "Promise.all()", "Promise.allSettled()", and "Promise.race()"

---

## 💡 Wisdom from Mímir

The single idea that made promises click for me was this: `.then()` doesn't run your callback and hand you the result — it runs your callback and hands you a *new promise wrapping* the result. Once you see chaining as "building a pipeline of promises, each one waiting on the last," the whole API stops feeling like magic and starts feeling like simple, predictable plumbing.

If you only remember one distinction from this note, make it `Promise.all()` vs `Promise.allSettled()` — I've seen more than one production incident where an entire batch job aborted because a single non-critical request failed inside a `Promise.all()`, when `allSettled()` was what the situation actually called for. Ask yourself up front: "if one of these fails, should the others still matter?" The answer picks the method for you.

---

## 🔗 Related Notes

- [[Async/Await]] — the syntax sugar built directly on top of promises, for writing async chains that read like synchronous code
- [[Callback Functions and Callback Hell]] — the exact problem promises were designed to solve
- [[The Event Loop: Call Stack, Task Queue, and Microtasks]] — explains precisely *when* promise callbacks actually run relative to other code
- [[The Fetch API for HTTP Requests]] — the most common real-world source of promises in everyday web development
- [[Error Handling with try/catch]] — how `try`/`catch` interacts with promise rejections once you're using `async`/`await`
