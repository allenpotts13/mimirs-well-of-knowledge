---
type: concept
status: active
created: 2026-09-02
updated: 2026-09-02
technology: JavaScript
difficulty: Intermediate
tags:
  - this
  - functions
  - fundamentals
  - call-apply-bind
aliases:
  - this
  - What is this in JavaScript
  - Understanding this
---

# 📚 The this Keyword

> *`this` isn't decided by where a function is written — it's decided by how the function gets called.*

---

## 🎯 Purpose

`this` is probably the single most-asked-about "why doesn't this work" topic in JavaScript, because it breaks the assumption most people carry over from other languages: that a variable's meaning is fixed by where it's defined. `this` exists to let a single function definition behave differently depending on the object it's invoked through — which is powerful, but only if you know the rules for how it gets assigned at call time.

---

## 🧠 Key Ideas

- `this` is determined by the **call-site** — the specific way a function is invoked — not by where the function was written in the code.
- A **plain function call** (`greet()`) sets `this` to `undefined` in strict mode (or the global object in non-strict, "sloppy" mode).
- A **method call** (`obj.greet()`) sets `this` to the object the method was called *on* — `obj` — regardless of where `greet` was originally defined.
- A call with **`new`** (`new Person()`) creates a brand-new object and sets `this` to that new object inside the constructor function.
- **Arrow functions are the major exception**: they have no `this` of their own at all. Instead, they inherit `this` lexically from whatever scope they were *defined* in — the same way closures capture variables.
- `call()`, `apply()`, and `bind()` are the explicit tools for overriding what `this` a function uses, regardless of how it's later invoked.

---

## ⚙️ How It Works

The cleanest way to reason about `this` in a regular function is to ignore the function's definition entirely and look only at the moment it's called — specifically, what's immediately to the left of the parentheses. If there's an object and a dot right before the call (`user.login()`), `this` is that object. If it's a bare function name with nothing before it (`login()`), `this` falls back to `undefined` (strict mode) or the global object. If the call is preceded by `new`, JavaScript builds a fresh object first and binds `this` to it before running the constructor body. Same function, three different call-sites, three different values of `this` — that's the whole rule.

Arrow functions opt out of this system completely. Instead of getting a `this` assigned at call time, they simply reuse whatever `this` already existed in the surrounding code where they were written — exactly like a closure capturing a normal variable. This makes arrow functions extremely useful *inside* methods or callbacks where you want `this` to stay pinned to the outer context instead of shifting based on how the inner function happens to get invoked (see [[Arrow Functions vs Regular Functions]] for the full comparison).

When you need to force a specific `this` onto a regular function regardless of its call-site, `call()` and `apply()` invoke the function immediately with a `this` you specify (differing only in how they pass arguments — a list vs. an array), while `bind()` returns a *new* function permanently locked to that `this`, ready to be called later.

---

## 💻 Examples

```javascript
// this depends on the call-site, not the definition
function whoAmI() {
  console.log(this);
}

const user = { name: "Ada", whoAmI };

whoAmI();        // undefined (strict mode) — plain call, no object before the dot
user.whoAmI();    // { name: "Ada", whoAmI: [Function] } — called AS a method of user
```

```javascript
// The classic gotcha: losing `this` when detaching a method
const counter = {
  count: 0,
  increment() {
    this.count++;
    console.log(this.count);
  },
};

counter.increment();          // 1 — this === counter

const detached = counter.increment;
detached();                    // TypeError: Cannot read properties of undefined
// Called with no object before the dot, so `this` is undefined in strict mode
```

```javascript
// Arrow functions inherit `this` from their surrounding scope — no call-site rule
const timer = {
  seconds: 0,
  start() {
    setInterval(() => {
      // Arrow function has no own `this` — it uses start()'s `this`, i.e. `timer`
      this.seconds++;
      console.log(this.seconds);
    }, 1000);
  },
};
timer.start(); // logs 1, 2, 3... correctly incrementing timer.seconds

// Compare: a regular function here would lose `this` entirely
const brokenTimer = {
  seconds: 0,
  start() {
    setInterval(function () {
      this.seconds++; // `this` here is NOT brokenTimer — it's undefined/global
      console.log(this.seconds); // NaN or crashes
    }, 1000);
  },
};
```

```javascript
// call, apply, and bind — explicitly controlling this
function introduce(greeting) {
  console.log(`${greeting}, I'm ${this.name}`);
}

const person = { name: "Grace" };

introduce.call(person, "Hi");        // "Hi, I'm Grace" — args passed individually
introduce.apply(person, ["Hello"]);  // "Hello, I'm Grace" — args passed as an array

const boundIntroduce = introduce.bind(person);
boundIntroduce("Hey");                // "Hey, I'm Grace" — this is locked in permanently
```

---

## 🚀 Real World Applications

- Event handlers in the DOM: `element.addEventListener("click", handler)` calls `handler` with `this` set to `element` — a common source of confusion when the handler is an arrow function that instead uses the outer `this`.
- React class components historically required `this.handleClick = this.handleClick.bind(this)` in constructors precisely because passing a method as a callback strips it from its object, losing `this`.
- `setTimeout`/`setInterval` callbacks written as regular functions lose their surrounding `this`, which is why arrow function callbacks are now the default idiom.
- `call`/`apply` are used to borrow array methods on array-like objects (e.g. `Array.prototype.slice.call(arguments)` in older code) that aren't real arrays.
- Library and framework internals (Express middleware, jQuery event handlers, DOM APIs) frequently set `this` deliberately to give handler functions convenient access to the relevant object.

---

## ⚖️ Advantages

- Dynamic `this` lets a single method definition be reused across many different objects (via prototypes or `call`/`apply`) instead of hardcoding which object it works on.
- Arrow functions solve the "losing `this` in a callback" problem cleanly, without needing `.bind()` or a `const self = this` workaround.
- `call`, `apply`, and `bind` give precise, explicit control when the default call-site rules don't produce the `this` you need.

---

## ⚠️ Limitations

- The call-site rule is easy to state but genuinely easy to violate by accident — passing a method as a bare callback is one of the most common bugs in JavaScript.
- Arrow functions being unable to have their own `this` means they're the *wrong* choice for object methods or prototype methods that need to refer to the calling object dynamically.
- Debugging `this`-related bugs often requires mentally tracing the exact call-site, which can be non-obvious in code with lots of indirection (callbacks passed through several layers).

---

## 🚨 Common Mistakes

- Passing an object method as a callback (`element.addEventListener("click", obj.method)`) without binding it, then being surprised `this` inside isn't `obj` anymore.
- Using a regular function as a callback inside a method when the intent was to keep `this` referring to the outer object — arrow functions are usually the fix.
- Using an arrow function *as* an object method (`const obj = { greet: () => { console.log(this) } }`) and expecting `this` to be `obj` — it won't be, because arrow functions never bind their own `this`.
- Forgetting that `new` changes everything — calling a constructor function without `new` silently gives the wrong `this` (or throws, in strict mode/classes) instead of creating a new object.
- Assuming `this` behaves like a normal lexically-scoped variable, the way closures work — it doesn't, until you're inside an arrow function.

---

## 📖 Further Reading

- MDN: "this"
- *You Don't Know JS: this & Object Prototypes* (Kyle Simpson)
- MDN: "Function.prototype.call()", "apply()", and "bind()"

---

## 💡 Wisdom from Mímir

Stop trying to figure out `this` by staring at where a function is *defined* — I promise you'll never get a consistent answer that way. Look only at the call-site: what's immediately to the left of the parentheses when the function is actually invoked. That one habit resolves the vast majority of `this` confusion I've ever seen, in my own code and in code review.

The single most common real-world `this` bug I fix in other people's code is a method handed off as a bare callback — `setTimeout(obj.method, 1000)` or `addEventListener("click", obj.method)` — where the object gets silently dropped at the call-site. Either wrap it in an arrow function, or `.bind()` it explicitly; don't just hand off a method and hope it remembers who it belongs to.

---

## 🔗 Related Notes

- [[Arrow Functions vs Regular Functions]] — the full breakdown of why arrow functions have no own `this` and when that matters
