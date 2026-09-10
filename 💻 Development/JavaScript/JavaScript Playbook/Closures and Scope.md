---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: JavaScript
difficulty: Intermediate
tags:
  - closures
  - scope
  - functions
  - fundamentals
aliases:
  - Closures
  - Lexical Scope
  - What is a Closure
publish: true
---

# 📚 Closures and Scope

> *A closure is a function that remembers the variables from the place it was defined, even after that outer place has finished running.*

---

## 🎯 Purpose

"Closures" have a reputation for being one of the harder JavaScript concepts, but every JavaScript developer uses them constantly — often without realizing it. This concept exists to explain *why* an inner function can still see a variable from a function call that already returned, because that behavior looks like magic until you understand lexical scope.

---

## 🧠 Key Ideas

- **Lexical scope** means a function's access to variables is determined by *where it is written in the code*, not by how or when it's called.
- A **closure** forms whenever a function is defined inside another function and references variables from that outer function — the inner function "closes over" those variables.
- The outer function's variables stay alive in memory as long as *any* inner function still references them, even after the outer function has returned.
- Each *call* to the outer function creates a **new, independent** set of variables — and a new closure — even though it's the same function definition each time.
- Closures are not a special syntax — they happen automatically any time a nested function is defined; there's no separate "closure" keyword.

---

## ⚙️ How It Works

Think of a function call as a room that gets built when the function starts running, furnished with its local variables, and normally torn down when the function returns. A closure is what happens when you hand someone a **key to that room** before it gets torn down — specifically, by returning (or otherwise keeping a reference to) an inner function that was defined inside it. As long as that key exists anywhere, JavaScript keeps the room standing instead of tearing it down, even though the outer function itself already finished running.

---

## 💻 Examples

```javascript
function makeCounter() {
  let count = 0; // lives inside makeCounter's "room"

  return function () {
    count += 1; // the inner function has a key to that room
    return count;
  };
}

const counterA = makeCounter();
const counterB = makeCounter();

console.log(counterA()); // 1
console.log(counterA()); // 2
console.log(counterB()); // 1 — a completely separate "room" and count
```

`counterA` and `counterB` each closed over their *own* `count` variable from their *own* call to `makeCounter()`. They don't share state, even though they came from the same function definition.

```javascript
// The classic loop-variable gotcha
for (var i = 1; i <= 3; i++) {
  setTimeout(() => console.log(i), 0);
}
// Logs: 4, 4, 4 — because var is function-scoped, so all three
// closures share the SAME i, which has already reached 4 by the time
// the callbacks run.

for (let j = 1; j <= 3; j++) {
  setTimeout(() => console.log(j), 0);
}
// Logs: 1, 2, 3 — let is block-scoped, so each loop iteration gets
// its own separate j, and each closure captures a different one.
```

```javascript
// A practical closure: a private variable no outside code can touch directly
function createBankAccount(startingBalance) {
  let balance = startingBalance; // "private" — no way to reach it except below

  return {
    deposit(amount) { balance += amount; return balance; },
    withdraw(amount) { balance -= amount; return balance; },
    getBalance() { return balance; },
  };
}

const account = createBankAccount(100);
account.deposit(50);
console.log(account.getBalance()); // 150
// There is no `account.balance` — the only access is through the closure's methods
```

---

## 🚀 Real World Applications

- **Data privacy / module pattern**: hiding internal state so it can only be changed through controlled methods (the bank-account example above).
- **Event handlers and callbacks**: a click handler defined inside a component function closes over that component's specific props/state at the time it was created.
- **Function factories**: `makeCounter`-style functions that generate multiple independent, pre-configured functions (e.g. `multiplyBy(3)`, `multiplyBy(10)`).
- **Debouncing and throttling**: both rely on a closure holding onto a timer ID or "last called" timestamp between invocations.
- **`useState`/`useEffect` in React**: React hooks are built entirely on closures capturing a specific render's variables.

---

## ⚖️ Advantages

- Enables genuinely private state in JavaScript without needing classes or special syntax.
- Lets you generate specialized functions from a general one without repeating code.
- Makes callback-heavy code (event listeners, timers, async operations) naturally "remember" the context they were created in.

---

## ⚠️ Limitations

- Every variable a closure captures stays in memory for as long as the closure exists — long-lived closures over large objects can cause real memory retention if not cleaned up (e.g. event listeners never removed).
- Can make debugging harder, since a variable's value depends on *which* closure/call created it, not just its name.
- Overusing closures for "private" state can make testing harder than a more explicit class or module approach, since there's no direct external access to inspect internal values.

---

## 🚨 Common Mistakes

- Using `var` in a loop that creates closures (e.g. `setTimeout` inside a `for` loop) and being surprised every callback sees the same final value — use `let` or an IIFE to give each iteration its own binding.
- Assuming closures "copy" a variable's value at creation time — they actually keep a *live reference*, so if the outer variable changes later, the closure sees the new value.
- Accidentally creating a new closure on every render/call (e.g. defining a function inside a React component) when it wasn't needed, causing unnecessary re-renders or memory churn.
- Forgetting to clean up closures held by event listeners or timers, leading to memory leaks in long-running applications.

---

## 📖 Further Reading

- MDN: "Closures"
- *You Don't Know JS: Scope & Closures* (Kyle Simpson)
- MDN: "var, let, const" for the scoping distinction that closures depend on

---

## 💡 Wisdom from Mímir

The moment closures actually clicked for me was realizing they aren't a special feature you opt into — they're just what already happens whenever a function is defined inside another function. The "special" part is only that JavaScript keeps outer variables alive for as long as an inner function might still need them, instead of throwing them away the instant the outer function returns.

Whenever a bug looks like "this variable has the wrong value" inside a callback, my first question now is: *which call, and which specific closure, produced this callback?* Almost every `var`-in-a-loop bug and every "stale state in my event handler" bug in a UI framework traces back to that one question.

---

## 🔗 Related Notes

- [[var vs let vs const]] — the block-vs-function scoping distinction that makes the loop-variable closure gotcha possible
- [[The Event Loop - Call Stack, Task Queue, and Microtasks]] — explains *when* a closure like a `setTimeout` callback actually runs
- [[Higher-Order Functions]] — closures are what make returning specialized functions from a general one possible
- [[Debouncing and Throttling]] — a concrete, practical application of a closure holding state between calls
