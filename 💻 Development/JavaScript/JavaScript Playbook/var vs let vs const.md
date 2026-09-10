---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: JavaScript
difficulty: Beginner
tags:
  - variables
  - scope
  - fundamentals
  - es6
aliases:
  - var let const
  - Variable Declarations
  - Block Scope vs Function Scope
publish: true
---

# 📚 var vs let vs const

> *`var` is function-scoped and forgiving to a fault; `let` and `const` are block-scoped and will actually stop you from using a variable before it exists.*

---

## 🎯 Purpose

JavaScript has three ways to declare a variable, and they exist because the language grew up in public. `var` is the original, dating back to 1995, and its looseness caused enough real bugs that `let` and `const` were added in ES6 (2015) specifically to fix `var`'s scoping quirks. Understanding *why* there are three keywords — and not just which one is "modern" — is what lets you read fifteen years of JavaScript code and know exactly what each declaration will and won't let you do.

---

## 🧠 Key Ideas

- **`var` is function-scoped**: it ignores block boundaries entirely. A `var` declared inside an `if` block or a `for` loop is visible to the *entire enclosing function*, not just that block.
- **`let` and `const` are block-scoped**: they only exist inside the nearest pair of `{ }` they were declared in — an `if`, a `for` loop, a bare block, whatever.
- All three are technically **hoisted** (their declarations are processed before code runs), but they behave differently before their declaration line: `var` is usable early and reads as `undefined`, while `let`/`const` sit in the **temporal dead zone** and throw a `ReferenceError` if touched early.
- **`const` does not mean immutable** — it only means the *variable binding* can't be reassigned to point at something else. If the value is an object or array, its contents can still be freely mutated.
- `var` also allows **redeclaring** the same variable name in the same scope without error; `let`/`const` throw a `SyntaxError` if you try to redeclare them.
- Modern style defaults to `const` for everything, switching to `let` only when a variable genuinely needs to be reassigned, and treats `var` as effectively legacy syntax you read but don't write.

---

## ⚙️ How It Works

Picture function scope as one big shared room and block scope as a series of smaller rooms nested inside it — an `if` block, a `for` loop body, a plain `{ }`. A `var` declared anywhere inside the building is treated as if it were declared at the *entrance to the whole building* (the function): it doesn't matter which small room you wrote it in, everyone in the building can see it. A `let` or `const`, on the other hand, only exists inside the specific small room it was declared in — step outside that room's `{ }` and it's gone, exactly the way you'd expect a normal variable to behave.

The temporal dead zone is the stretch of code between the top of a scope and the actual `let`/`const` declaration line. The variable technically exists in that zone (JavaScript already knows its name), but it's locked — trying to read it throws an error instead of silently giving you `undefined`. This is a deliberate safety feature: it turns "I used a variable before I meant to" from a silent bug into a loud, immediate crash.

---

## 💻 Examples

```javascript
// var leaks out of blocks — function scope, not block scope
function checkVar() {
  if (true) {
    var message = "I escaped the if block";
  }
  console.log(message); // "I escaped the if block" — still accessible here
}
checkVar();

// let/const respect block boundaries
function checkLet() {
  if (true) {
    let message = "I'm trapped in this block";
  }
  console.log(message); // ReferenceError: message is not defined
}
```

```javascript
// The temporal dead zone in action
console.log(usesVar); // undefined — var is hoisted AND initialized to undefined
var usesVar = "hi";

console.log(usesLet); // ReferenceError: Cannot access 'usesLet' before initialization
let usesLet = "hi";
```

```javascript
// const prevents reassignment, NOT mutation
const scores = [10, 20, 30];
scores.push(40);        // fine — mutating the array's contents
scores[0] = 99;          // fine — mutating an element
console.log(scores);     // [99, 20, 30, 40]

scores = [1, 2, 3];      // TypeError: Assignment to constant variable.
// The binding "scores" can never point to a different array,
// but the array it already points to is fully mutable.

const user = { name: "Ada" };
user.name = "Grace";     // fine — mutating a property
user.age = 36;           // fine — adding a new property
console.log(user);       // { name: "Grace", age: 36 }
```

```javascript
// var's classic loop bug — one shared variable across all iterations
for (var i = 1; i <= 3; i++) {
  setTimeout(() => console.log("var:", i), 0);
}
// Logs: var: 4, var: 4, var: 4

// let gives each iteration its own binding
for (let j = 1; j <= 3; j++) {
  setTimeout(() => console.log("let:", j), 0);
}
// Logs: let: 1, let: 2, let: 3
```

---

## 🚀 Real World Applications

- Loop counters in `for` loops that spawn asynchronous callbacks (timers, event listeners, promises) almost always need `let`, not `var`, to avoid the shared-variable bug.
- `const` is the default choice for imported modules, configuration objects, and React component references — anything that shouldn't be reassigned but whose contents may still change.
- Linters (ESLint's `no-var` and `prefer-const` rules) are standard in professional codebases specifically to eliminate `var` and enforce `const`-by-default habits.
- Reading and maintaining older JavaScript code (pre-2015, or codebases with legacy conventions) requires understanding `var`'s function-scoping to correctly predict its behavior.
- Deliberately using `var`'s function-scoping in rare cases — e.g., a value that's genuinely meant to persist beyond a block — though `let` declared outside the block is almost always the clearer choice today.

---

## ⚖️ Advantages

- `let`/`const` catch a whole category of scoping bugs at the moment they happen (via the temporal dead zone and redeclaration errors) instead of letting them fail silently.
- Block scoping matches how every other mainstream language works, making JavaScript's scoping rules far less surprising to newcomers.
- Defaulting to `const` documents intent directly in the code — a reader immediately knows a binding won't be reassigned, without having to trace the whole function.

---

## ⚠️ Limitations

- Switching legacy code from `var` to `let`/`const` isn't always a safe find-and-replace — code that relied on `var`'s function-scoped leakage can break.
- `const` gives a false sense of security to newcomers who assume it means "frozen" — it doesn't, and reaching for `Object.freeze()` is a separate, deliberate step.
- Block scoping means the same variable name can be legally reused across sibling blocks, which can occasionally make code harder to search for by name.

---

## 🚨 Common Mistakes

- Assuming `const` makes an object or array immutable — it doesn't; use `Object.freeze()` if true immutability is required.
- Using `var` inside a loop that creates closures (event handlers, `setTimeout`) and being confused when every callback reports the same final value.
- Expecting `let`/`const` to behave like undeclared variables before their line runs — forgetting the temporal dead zone throws instead of returning `undefined`.
- Declaring the same `let`/`const` name twice in the same scope out of habit from `var`-era code, and being surprised by a `SyntaxError`.
- Thinking hoisting only applies to `var` — `let`/`const` are hoisted too, they're just left uninitialized instead of usable early.

---

## 📖 Further Reading

- MDN: "var", "let", and "const" reference pages
- MDN: "Grammar and types — Variables"
- *You Don't Know JS: Scope & Closures* (Kyle Simpson)

---

## 💡 Wisdom from Mímir

The rule I give every junior engineer is simple: reach for `const` first, always. If the code won't compile because you need to reassign, upgrade to `let`. You should almost never *start* with `var` — and if you're reading code that uses it, treat every `var` as a signal to slow down and check its full function scope, not just the block it appears in.

The `const`-means-immutable misconception is one of the most common I see in code review, usually showing up as a bug where someone mutates a "constant" array in one function and is baffled when it affects code elsewhere. `const` protects the *label*, not the *thing the label points to* — internalize that distinction once and it never trips you up again.

---

## 🔗 Related Notes

- [[Closures and Scope]] — the block-vs-function scoping distinction here is exactly what makes the classic `var`-in-a-loop closure bug possible
- [[Hoisting]] — the deeper mechanics of how all three declaration types are processed before code runs, and why `let`/`const` land in the temporal dead zone
