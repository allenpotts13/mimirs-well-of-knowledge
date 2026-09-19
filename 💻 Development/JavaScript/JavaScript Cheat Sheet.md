---
publish: true
status: published
permalink: javascript/javascript-cheat-sheet
---

# <span class="rune">ᛊ</span> JavaScript Cheat Sheet

> Dense reference across core JavaScript. Each section links back to the full [[JavaScript Codex|concept note]] for depth.

---

## Variables & Types

```javascript
var x = 1;      // function-scoped, hoisted, avoid in modern code
let y = 2;      // block-scoped, reassignable
const z = 3;    // block-scoped, cannot be reassigned (but object/array CONTENTS still mutable)

typeof undefined      // "undefined"
0 == "0"              // true  — coercion happens first
0 === "0"             // false — no coercion, ALWAYS prefer ===

// Falsy values — everything else is truthy
false, 0, -0, 0n, "", null, undefined, NaN
```

See: [[var vs let vs const]] · [[Truthy and Falsy Values]] · [[== vs === and Type Coercion]]

---

## Hoisting

```javascript
console.log(a);   // undefined — declaration hoisted, NOT the assignment
var a = 5;

console.log(b);    // ReferenceError — in the "temporal dead zone"
let b = 5;

foo();              // works — function DECLARATIONS are fully hoisted
function foo() {}

bar();              // TypeError — function EXPRESSIONS are not
const bar = function () {};
```

See: [[Hoisting]]

---

## Functions

```javascript
function regular(a, b) { return a + b; }        // has its OWN "this", "arguments"
const arrow = (a, b) => a + b;                   // inherits "this" LEXICALLY from enclosing scope
const arrowBlock = (a, b) => { return a + b; };  // braces require explicit return

// "this" pitfall — classic reason to prefer arrow functions in callbacks
class Timer {
    start() {
        setTimeout(function () { this.tick(); }, 1000);   // "this" is WRONG here (not Timer)
        setTimeout(() => { this.tick(); }, 1000);          // "this" is CORRECT — lexically inherited
    }
}
```

See: [[Arrow Functions vs Regular Functions]] · [[The this Keyword]]

---

## Template Literals & Strings

```javascript
const name = "World";
`Hello, ${name}!`              // string interpolation
`Line 1
Line 2`                        // multi-line, no \n needed
`${1 + 1} is two`               // any expression works inside ${}
```

See: [[Template Literals]]

---

## Arrays vs Objects

```javascript
const arr = [1, 2, 3];              // ordered, indexed, duplicates allowed
const obj = { name: "Bo", age: 30 }; // key-value, unordered (mostly), unique keys

Array.isArray(arr);                  // true — the reliable way to check
```

**Destructuring & spread/rest:**

```javascript
const [first, second, ...rest] = [1, 2, 3, 4];   // first=1, second=2, rest=[3,4]
const { name, age } = obj;                        // pulls out by KEY, order doesn't matter
const { name: n = "Anon" } = obj;                 // rename + default value

const merged = { ...obj, city: "NYC" };            // spread — shallow copy + override
const combined = [...arr, 4, 5];
```

See: [[Arrays vs Objects - When to Use Which]] · [[Object Destructuring and the Spread-Rest Operators]]

---

## Array Methods

```javascript
[1, 2, 3].map(x => x * 2);             // [2, 4, 6] — transform, SAME length
[1, 2, 3].filter(x => x > 1);          // [2, 3]    — keep matching, length <=
[1, 2, 3].reduce((acc, x) => acc + x, 0); // 6      — fold to a SINGLE value

[1, 2, 3].forEach(x => console.log(x)); // no return value — side effects only
[1, 2, 3].find(x => x > 1);              // 2 — first match, or undefined
[1, 2, 3].some(x => x > 2);              // true — at least one matches
[1, 2, 3].every(x => x > 0);             // true — all match
[1, 2, 3].includes(2);                   // true
```

`map`/`filter`/`reduce` all return NEW arrays/values — none mutate the original.

See: [[Array Methods - map, filter, and reduce]] · [[Higher-Order Functions]]

---

## Closures & Scope

```javascript
function makeCounter() {
    let count = 0;                  // "closed over" — stays alive after makeCounter() returns
    return () => ++count;
}
const counter = makeCounter();
counter(); // 1
counter(); // 2 — SAME closure, state persists

// Classic var-in-loop gotcha
for (var i = 1; i <= 3; i++) { setTimeout(() => console.log(i), 0); }  // 4, 4, 4 — shared var
for (let j = 1; j <= 3; j++) { setTimeout(() => console.log(j), 0); }  // 1, 2, 3 — new binding per iteration
```

See: [[Closures and Scope]]

---

## Prototypes

```javascript
function Animal(name) { this.name = name; }
Animal.prototype.speak = function () { console.log(this.name + " makes a sound"); };

const dog = new Animal("Rex");
dog.speak();                          // found via the PROTOTYPE CHAIN, not on dog itself
dog.hasOwnProperty("speak");           // false — it's inherited, not own

class Animal2 {                        // modern syntax — same prototype mechanism underneath
    constructor(name) { this.name = name; }
    speak() { console.log(this.name); }
}
```

See: [[Prototypes and Prototypal Inheritance]]

---

## Modules

```javascript
// utils.js
export const PI = 3.14;
export function add(a, b) { return a + b; }
export default function main() { }        // ONE default export per file

// app.js
import main, { PI, add } from "./utils.js";
import * as utils from "./utils.js";        // namespace import
```

See: [[The Module System - import and export]]

---

## JSON

```javascript
JSON.stringify({ a: 1, b: [2, 3] });   // '{"a":1,"b":[2,3]}' — object → string
JSON.parse('{"a":1}');                  // { a: 1 } — string → object

JSON.stringify(obj, null, 2);            // pretty-printed, 2-space indent
// undefined, functions, and symbols are SILENTLY DROPPED by stringify
```

See: [[JSON - Parsing and Stringifying]]

---

## Callbacks, Promises, Async/Await

```javascript
// Callback — the original async pattern, prone to "callback hell" when nested
getUser(id, (user) => {
    getOrders(user, (orders) => {
        console.log(orders);   // nesting grows sideways with each dependent step
    });
});

// Promise — flattens the nesting, adds real error propagation
getUser(id)
    .then(user => getOrders(user))
    .then(orders => console.log(orders))
    .catch(err => console.error(err));

// async/await — same Promises underneath, reads like synchronous code
async function loadOrders(id) {
    try {
        const user = await getUser(id);
        const orders = await getOrders(user);
        console.log(orders);
    } catch (err) {
        console.error(err);
    }
}

Promise.all([p1, p2, p3]);        // waits for ALL — rejects if ANY rejects
Promise.allSettled([p1, p2, p3]); // waits for ALL — never short-circuits, gives status per promise
Promise.race([p1, p2]);            // resolves/rejects with whichever settles FIRST
```

See: [[Callback Functions and Callback Hell]] · [[Promises]] · [[Async-Await]]

---

## The Event Loop

```text
Call Stack (sync code) → runs first, always
Microtask Queue (Promise .then/.catch/.finally, queueMicrotask) → runs NEXT, fully drained
Task Queue / Macrotask (setTimeout, setInterval, I/O) → runs LAST, one per loop tick
```

```javascript
console.log("1");
setTimeout(() => console.log("2"), 0);   // macrotask — LAST
Promise.resolve().then(() => console.log("3")); // microtask — BEFORE the macrotask
console.log("4");
// Output: 1, 4, 3, 2
```

See: [[The Event Loop - Call Stack, Task Queue, and Microtasks]]

---

## Fetch API

```javascript
fetch("/api/users")
    .then(res => {
        if (!res.ok) throw new Error("HTTP " + res.status); // fetch does NOT reject on 4xx/5xx!
        return res.json();
    })
    .then(data => console.log(data))
    .catch(err => console.error(err));

// async/await equivalent
async function getUsers() {
    const res = await fetch("/api/users");
    if (!res.ok) throw new Error("HTTP " + res.status);
    return res.json();
}
```

See: [[The Fetch API for HTTP Requests]]

---

## DOM & Events

```javascript
document.querySelector(".item");           // first match
document.querySelectorAll(".item");        // NodeList of all matches

el.addEventListener("click", (e) => { });
el.textContent = "text";                    // safe — no HTML parsing
el.innerHTML = "<b>html</b>";                // parses HTML — XSS risk with untrusted input

// Event delegation — ONE listener on a parent, instead of one per child
parent.addEventListener("click", (e) => {
    if (e.target.matches(".item")) { /* handle */ }
});
```

See: [[DOM Manipulation Basics]] · [[Event Handling and Event Delegation]]

---

## Debouncing & Throttling

```javascript
function debounce(fn, delay) {
    let timer;
    return (...args) => {
        clearTimeout(timer);
        timer = setTimeout(() => fn(...args), delay); // waits for a PAUSE in calls
    };
}

function throttle(fn, limit) {
    let waiting = false;
    return (...args) => {
        if (waiting) return;
        fn(...args);
        waiting = true;
        setTimeout(() => waiting = false, limit);      // runs at most once per interval
    };
}
```

**Debounce** = wait for silence (search-as-you-type). **Throttle** = steady rate cap (scroll/resize handlers).

See: [[Debouncing and Throttling]]

---

## Local Storage vs Session Storage

```javascript
localStorage.setItem("key", "value");    // persists across tabs AND browser restarts
sessionStorage.setItem("key", "value");  // cleared when the TAB closes

localStorage.getItem("key");
localStorage.removeItem("key");
localStorage.setItem("obj", JSON.stringify({ a: 1 })); // both APIs store STRINGS only
```

See: [[Local Storage vs Session Storage]]

---

## Error Handling

```javascript
try {
    riskyOperation();
} catch (err) {
    console.error(err.message);
} finally {
    cleanup(); // always runs
}

throw new Error("custom message");   // prefer Error objects over throwing plain strings
```

See: [[Error Handling with try-catch]]

---

## 🔗 Related Notes

- [[JavaScript Codex]]
- [[Java Cheat Sheet]]
