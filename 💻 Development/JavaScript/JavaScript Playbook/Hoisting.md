---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: JavaScript
difficulty: Intermediate
tags:
  - hoisting
  - scope
  - fundamentals
  - temporal-dead-zone
aliases:
  - Hoisting in JavaScript
  - Temporal Dead Zone
  - TDZ
publish: true
---

# 📚 Hoisting

> *Hoisting is JavaScript setting up all the names in a scope before running a single line of it — but "setting up" doesn't mean "ready to use."*

---

## 🎯 Purpose

Hoisting explains a genuinely confusing pattern beginners hit almost immediately: calling a function before the line where it's written works fine, but doing the same thing with a variable sometimes gives `undefined` and sometimes crashes outright. This concept exists to demystify that behavior — it's not random, it's a completely mechanical process JavaScript runs before executing any code, and once you know the rule for each declaration type, the behavior stops looking inconsistent.

---

## 🧠 Key Ideas

- Before running any code in a scope, JavaScript scans it and registers every declaration it finds — this scanning pass is what "hoisting" refers to.
- **Function declarations** (`function greet() {}`) are hoisted *completely*, body and all — you can call them anywhere in their scope, even above the line they're written on.
- **`var` declarations** are hoisted but only the declaration, not the assignment — the name is registered and initialized to `undefined` immediately, and the actual value isn't assigned until the original line of code runs.
- **`let` and `const`** are hoisted too — the name is registered — but they stay **uninitialized** in what's called the "temporal dead zone" (TDZ) until their declaration line actually executes. Touching them before that throws a `ReferenceError`, not `undefined`.
- **Function expressions and arrow functions** assigned to a variable (`const greet = () => {}`) are *not* hoisted the same way as function declarations — only the variable part hoists (per `var`/`let`/`const` rules above), so calling `greet()` before that line fails, either with "undefined is not a function" (`var`) or a TDZ `ReferenceError` (`let`/`const`).

---

## ⚙️ How It Works

Think of JavaScript running each scope in two passes instead of one. The first pass is a quick registration sweep: it walks through the scope and writes down every name it's going to need — every `function`, every `var`, every `let`/`const` — like a teacher taking attendance before class starts. It doesn't do any of the actual work yet, it just makes sure every name has a seat reserved.

The second pass is the real one, executing your code top to bottom, line by line, exactly in the order you wrote it. This is where the difference between declaration types shows up. Function declarations got their *entire body* filled in during the registration pass, so they're fully usable immediately. `var` got a seat with a default placeholder value of `undefined`, so it's technically "there" but empty until its line runs. `let` and `const` got a seat too, but that seat is roped off — visible on the roster, but you're not allowed to use it — until the moment their declaration line actually executes. That roped-off period, from the top of the scope to the declaration line, is the temporal dead zone.

Function *expressions* and arrow functions are really just "a variable that happens to hold a function." Because they're variables first, they follow the hoisting rule for whatever keyword declared them (`var`, `let`, or `const`) — the function itself never gets the special full-body hoisting treatment that a plain `function` declaration gets.

---

## 💻 Examples

```javascript
// Function declarations hoist completely — this works
sayHi(); // "Hi!"

function sayHi() {
  console.log("Hi!");
}
```

```javascript
// var hoists the declaration, not the value
console.log(count); // undefined — registered, but not yet assigned
var count = 10;
console.log(count); // 10 — now the assignment line has run
```

```javascript
// let/const are hoisted but locked in the temporal dead zone
console.log(total); // ReferenceError: Cannot access 'total' before initialization
let total = 100;
```

```javascript
// Function expressions/arrow functions follow variable rules, not function rules
sayBye(); // TypeError: sayBye is not a function (it's undefined, not callable, at this point)

var sayBye = function () {
  console.log("Bye!");
};

// Same idea with const/let — but the error is different because of the TDZ
greetLater(); // ReferenceError: Cannot access 'greetLater' before initialization

const greetLater = () => {
  console.log("Hello later!");
};
```

```javascript
// A subtle mix: a var-declared name can be "shadowed" by hoisting confusion
function example() {
  console.log(typeof mystery); // "undefined" — var is hoisted, no error thrown
  var mystery = "solved";
}
example();

function exampleTwo() {
  console.log(typeof secret); // ReferenceError — TDZ applies even to typeof!
  let secret = "hidden";
}
```

---

## 🚀 Real World Applications

- Structuring files with helper `function` declarations below their usage (common in older codebases) relies entirely on function-declaration hoisting to work.
- Debugging "X is not a function" and "Cannot access before initialization" errors in real projects almost always comes down to a variable being used above where it's declared — recognizing the error message tells you immediately whether it's a `var`/expression issue or a TDZ issue.
- Linters like ESLint's `no-use-before-define` exist specifically to catch hoisting-related bugs before they reach production.
- Module-level code (ES modules, `import`/`export`) has its own hoisting nuances for imports, which behave similarly to function declarations — importable before the physical `import` line in execution order within the module graph.
- Understanding hoisting is essential for correctly predicting behavior when reading minified or bundled code, where declaration order can look scrambled.

---

## ⚖️ Advantages

- Function declaration hoisting allows organizing code with "main logic first, helper functions below" for readability, without needing forward declarations like some other languages require.
- The temporal dead zone turns a whole class of "used before declared" bugs into loud, immediate errors instead of silent `undefined` values that surface as confusing bugs much later.
- Once understood, hoisting rules are entirely mechanical and predictable — there's no ambiguity once you know the four declaration types' rules.

---

## ⚠️ Limitations

- The behavioral differences between `var`, `let`/`const`, and function declarations are a real source of subtle bugs for anyone who hasn't internalized all three rules.
- Relying on function hoisting to call a function before its definition can make code harder to read top-to-bottom, even though it's technically valid.
- The TDZ's error messages, while helpful, can still be confusing to beginners who don't yet know the term "temporal dead zone" exists.

---

## 🚨 Common Mistakes

- Assuming `let`/`const` aren't hoisted at all, because they don't behave like `var` — they *are* hoisted, they're just inaccessible until their line runs.
- Writing `const sayHi = function() {...}` and then calling `sayHi()` above that line, expecting it to work like a `function` declaration would.
- Using `typeof` on an undeclared-but-not-yet-initialized `let`/`const` variable expecting a safe `"undefined"` result — it throws instead, unlike `typeof` on a truly undeclared name.
- Forgetting that hoisting is scope-based, not file-based — a variable hoisted inside one function is not visible or hoisted in a sibling function.
- Writing code that "accidentally works" because of `var` hoisting, then being confused when refactoring it to `let`/`const` breaks it — this is usually a sign the original code relied on hoisting instead of being explicit about order.

---

## 📖 Further Reading

- MDN: "Hoisting"
- MDN: "let" — the Temporal Dead Zone section
- *You Don't Know JS: Scope & Closures* (Kyle Simpson)

---

## 💡 Wisdom from Mímir

Hoisting isn't magic and it isn't a JavaScript quirk to memorize as a list of exceptions — it's one consistent rule (everything gets registered first, execution happens second) applied to four different declaration types that each handle the gap between registration and execution differently. Learn the *mechanism* once, and every "why does this work but not that" question about ordering answers itself.

If I had to leave you with one practical habit: never rely on hoisting on purpose. Declare your variables and functions before you use them, every time, even when the language would technically let you get away with not doing so. The rules are consistent, but code that depends on them is always harder to read than code that just declares things in the order they're needed.

---

## 🔗 Related Notes

- [[var vs let vs const]] — the declaration-type differences that hoisting behavior directly depends on
