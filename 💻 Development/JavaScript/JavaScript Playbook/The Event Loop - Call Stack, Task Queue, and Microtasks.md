---
type: concept
status: active
created: 2026-09-02
updated: 2026-09-02
technology: JavaScript
difficulty: Advanced
tags:
  - event-loop
  - call-stack
  - asynchronous
  - microtasks
  - fundamentals
aliases:
  - "The Event Loop: Call Stack, Task Queue, and Microtasks"
  - Event Loop
  - Call Stack
  - Microtask Queue
  - Macrotask Queue
  - Task Queue
---

# 📚 The Event Loop: Call Stack, Task Queue, and Microtasks

> *The event loop is the mechanism that lets a single-threaded language like JavaScript handle asynchronous work by juggling one call stack, one microtask queue, and one (macro)task queue — in that priority order, forever.*

---

## 🎯 Purpose

JavaScript runs on a single thread — one call stack, one thing happening at a time. Yet JavaScript regularly does things that take an unpredictable amount of time (network requests, timers, file reads) without freezing the entire page while it waits. The event loop is the concept that explains how that's possible: it's the scheduling system that decides *when* deferred work — Promise callbacks, `setTimeout` callbacks, DOM events — actually gets to run, given that only one thing can run on the stack at any instant. Understanding it is the difference between guessing at async output order and knowing it.

---

## 🧠 Key Ideas

- JavaScript has **one call stack**. Whatever function is currently executing is on top of it; when it returns, it's popped off, and the next thing runs.
- **`setTimeout(fn, 0)` does not run `fn` immediately.** It schedules `fn` to run later, as a **macrotask**, and macrotasks only run once the call stack is completely empty — even a delay of `0` just means "as soon as possible after everything currently running finishes," not "right now."
- The **microtask queue** holds things like resolved Promise `.then()`/`.catch()`/`.finally()` callbacks and `queueMicrotask()` callbacks. It has **higher priority** than the macrotask (task) queue.
- After every single macrotask finishes (including the very first script run), the event loop **fully drains the entire microtask queue** — running every microtask, including new ones added by earlier microtasks — before it's allowed to pick up the next macrotask or repaint the UI.
- This priority ordering is *why* a resolved Promise's `.then()` reliably runs before a `setTimeout(fn, 0)`, even when both are "scheduled" at essentially the same moment — the microtask queue always empties first.

---

## ⚙️ How It Works

Picture three separate lines at a very particular restaurant. The **call stack** is the chef's cutting board — only one dish can be actively worked on at a time, and the chef won't start a new one until the current one is completely finished and off the board. The **microtask queue** is the VIP line — anyone in it gets served the *instant* the board is clear, and critically, if a VIP's order causes another VIP ticket to appear, that new ticket also gets served before the board is handed back to anyone else. The **task (macrotask) queue** is the regular line — it only gets a turn once the board is clear *and* the entire VIP line has been completely emptied.

Concretely: `console.log()` and any synchronous code run directly on the call stack, right now. `setTimeout()`, DOM events, and I/O callbacks go into the macrotask queue. `.then()`/`.catch()`/`.finally()` on an already-settled (or soon-to-settle) Promise go into the microtask queue. The event loop's job, forever, is: run the call stack until empty → drain the *entire* microtask queue (even ones added mid-drain) → run exactly *one* macrotask → drain the microtask queue again → repeat.

---

## 💻 Examples

```javascript
// The classic ordering puzzle
console.log("1: script start");

setTimeout(() => {
  console.log("2: setTimeout callback"); // macrotask — waits for the queue's turn
}, 0);

Promise.resolve().then(() => {
  console.log("3: promise .then callback"); // microtask — jumps the line
});

console.log("4: script end");

// Output order:
// 1: script start
// 4: script end
// 3: promise .then callback   <-- microtask runs before the macrotask...
// 2: setTimeout callback      <-- ...even though setTimeout was scheduled FIRST
```

```javascript
// Microtasks can keep spawning more microtasks and they ALL run
// before the next macrotask gets a turn
console.log("start");

setTimeout(() => console.log("macrotask"), 0);

Promise.resolve()
  .then(() => {
    console.log("microtask 1");
    return Promise.resolve(); // chaining creates ANOTHER microtask
  })
  .then(() => console.log("microtask 2"));

console.log("end");

// Output:
// start
// end
// microtask 1
// microtask 2   <-- still runs before "macrotask", because the queue
//                   isn't considered drained until it's truly empty
// macrotask
```

```javascript
// async/await is just microtask-queued Promise resolution in disguise
async function demo() {
  console.log("A: inside async function, before await");
  await null; // pauses here — the rest of the function becomes a microtask
  console.log("C: after await, resumed as a microtask");
}

console.log("start");
demo();
console.log("B: after calling demo()");

// Output: start, A, B, C
// "C" runs as a microtask AFTER the synchronous code finishes,
// even though there's no visible .then() in sight.
```

```javascript
// A common gotcha: a "0ms" timeout does not mean "instant"
console.log("before timeout");
setTimeout(() => console.log("inside timeout"), 0);
for (let i = 0; i < 1_000_000_000; i++) {} // a slow, blocking loop
console.log("after loop");

// Output: before timeout, after loop, inside timeout
// The blocking loop runs entirely on the call stack FIRST.
// The timeout callback can't run until the stack is empty,
// no matter how small its delay was.
```

---

## 🚀 Real World Applications

- Explains why a `setTimeout(fn, 0)` "escape hatch" is sometimes used to defer work until after the current render/microtask cycle finishes.
- Explains why chained `.then()` calls and `await`ed statements resolve in a predictable order relative to other scheduled work like UI events or timers.
- Directly relevant to debugging "why did this run in the wrong order" bugs in async-heavy code — animation frames, network responses, and timers.
- Explains why a long synchronous loop (a "blocking" operation) freezes the entire UI: nothing else — not even a click handler — can run until the call stack clears.
- Node.js has its own more elaborate event loop with additional phases (timers, I/O callbacks, `setImmediate`, close callbacks) built on this same core call-stack/microtask/macrotask idea.

---

## ⚖️ Advantages

- Lets a single-threaded language handle thousands of pending operations (timers, requests, events) without needing OS-level threads or explicit locks.
- The strict "drain all microtasks first" rule makes Promise-based code's ordering fully predictable and reasonable about, once you know the rule.
- Keeps the mental model simple: exactly one thing runs at a time, so there's no risk of two pieces of JavaScript executing simultaneously and racing over shared state.

---

## ⚠️ Limitations

- Because it's single-threaded, one long-running synchronous operation blocks *everything* — UI rendering, click handlers, network callbacks — until it finishes.
- Microtasks that keep scheduling more microtasks (a recursive `.then()` chain, for example) can starve the macrotask queue indefinitely, delaying timers and rendering.
- The exact queue mechanics differ subtly between browsers and Node.js (Node has additional queue phases like `process.nextTick` and `setImmediate`), so "the event loop" isn't 100% identical everywhere.

---

## 🚨 Common Mistakes

- Assuming `setTimeout(fn, 0)` runs "immediately" or "before" other scheduled code — it always waits for both the call stack and the entire microtask queue to clear first.
- Being surprised that a Promise `.then()` runs before a `setTimeout` scheduled earlier in the code — this is expected: microtasks always beat macrotasks, regardless of scheduling order.
- Writing a blocking synchronous loop (e.g. heavy computation) and being confused why the UI freezes or timers fire late — the call stack has to be empty before anything else gets a turn.
- Forgetting that `await` yields to the microtask queue, not the macrotask queue — code after an `await` runs sooner than people expect relative to `setTimeout`.
- Assuming Node.js and browsers schedule things identically — Node's additional phases (like `process.nextTick`, which runs even before other microtasks) can produce different orderings than a browser for the same-looking code.

---

## 📖 Further Reading

- MDN: "The event loop"
- Jake Archibald: "Tasks, microtasks, queues and schedules" (jakearchibald.com — a widely cited deep dive with interactive examples)
- Node.js docs: "The Node.js Event Loop, Timers, and process.nextTick()"

---

## 💡 Wisdom from Mímir

The event loop stops being mysterious the moment you stop thinking of "async" as "runs at some vague point later" and start thinking of it as "gets placed into one of exactly two ordered lines, both of which wait for the call stack to be empty." Once you can name which queue a piece of code lands in — microtask or macrotask — you can predict its exact position in the output, every time, no guessing required.

The bug I see most often from people who *think* they understand this: they assume `setTimeout(fn, 0)` means "run next." It doesn't mean "next" — it means "after the call stack clears, and after every microtask, including ones that don't exist yet when the timeout was scheduled, has had its turn." That gap is where a surprising number of production race conditions live.

---

## 🔗 Related Notes

- [[Promises]] — the mechanism whose `.then()`/`.catch()`/`.finally()` callbacks populate the microtask queue
- [[Async-Await]] — syntactic sugar that hides microtask scheduling behind normal-looking code
- [[Closures and Scope]] — explains why a `setTimeout` or Promise callback still has access to variables from where it was defined, even once it finally runs
- [[Callback Functions and Callback Hell]] — the earlier pattern for scheduling deferred work that the event loop also governs
