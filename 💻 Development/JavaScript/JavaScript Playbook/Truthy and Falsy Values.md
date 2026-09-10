---
type: concept
status: active
created: 2026-09-02
updated: 2026-09-06
technology: JavaScript
difficulty: Beginner
tags:
  - truthy-falsy
  - type-coercion
  - conditionals
  - fundamentals
aliases:
  - Falsy Values
  - Truthy Values
  - Boolean Coercion
---

# 📚 Truthy and Falsy Values

> *Every JavaScript value is either "truthy" or "falsy" — it behaves like `true` or `false` when JavaScript needs to treat it as a boolean, such as in an `if` condition.*

---

## 🎯 Purpose

JavaScript lets you write `if (value)` on *any* value — a string, a number, an object, `null` — without first converting it to a real boolean. That only works because the language defines exactly which values act like `false` when coerced to boolean, and treats absolutely everything else as acting like `true`. This concept exists so you can write short, idiomatic conditionals (`if (user)` instead of `if (user !== null && user !== undefined)`) — but only safely once you know the complete, short list of exceptions.

---

## 🧠 Key Ideas

- There are exactly **eight falsy values** in JavaScript, and memorizing this short list removes almost all the guesswork: `false`, `0`, `-0`, `0n` (BigInt zero), `""` (empty string), `null`, `undefined`, and `NaN`.
- **Everything else is truthy** — including values that "feel" like they should be empty or falsy.
- The classic surprises: the string `"0"` is truthy (it's a non-empty string, and string emptiness — not content — is what matters), an empty array `[]` is truthy, and an empty object `{}` is truthy. All objects, including empty ones, are truthy.
- Truthy/falsy checks happen automatically in `if`/`else`, `while`, ternaries (`? :`), and with the logical operators `&&`, `||`, and `!`.
- You can force an explicit boolean conversion with `Boolean(value)` or the double-negation shorthand `!!value` — both apply the exact same truthy/falsy rules, just make the result an actual `true`/`false` instead of leaving it implicit.
- Truthy/falsy is distinct from `==` equality to `true`/`false` — a truthy value doesn't necessarily `== true` (e.g. `"0" == true` is actually `false`, even though `"0"` is truthy), so don't try to substitute one concept for the other.

---

## ⚙️ How It Works

Picture JavaScript keeping a short, fixed "blocklist" of eight specific values it considers falsy. Whenever a value shows up somewhere that demands a boolean — the condition of an `if`, the test of a `while` loop, either side of `&&`/`||` — JavaScript checks that value against the blocklist. If it's on the list, it acts like `false`. If it's *not* on the list — no matter what it is, a string, a populated object, an empty array, a function — it acts like `true`. There's no separate "truthy list" to memorize, because truthy is simply defined as "not on the falsy list." That's why the falsy list is the one worth memorizing cold: it's short, fixed, and everything else follows from it by elimination.

---

## 💻 Examples

```javascript
// The complete list of falsy values — memorize these eight
if (false) {}      // falsy
if (0) {}          // falsy
if (-0) {}         // falsy
if (0n) {}         // falsy (BigInt zero)
if ("") {}         // falsy (empty string)
if (null) {}       // falsy
if (undefined) {}  // falsy
if (NaN) {}        // falsy

// None of the bodies above ever run.
```

```javascript
// The classic "feels empty but isn't" surprises — all TRUTHY
if ("0") console.log("truthy!");   // runs — non-empty string, content doesn't matter
if ([]) console.log("truthy!");    // runs — empty array is still an object
if ({}) console.log("truthy!");    // runs — empty object is still an object
if (" ") console.log("truthy!");   // runs — a single space is a non-empty string
if (Infinity) console.log("truthy!"); // runs — only NaN among numbers is falsy
if (-1) console.log("truthy!");    // runs — only 0 and -0 among numbers are falsy
```

```javascript
// Explicit conversion to a real boolean
console.log(Boolean(""));    // false
console.log(Boolean("hi"));  // true
console.log(!!0);            // false — double negation is a common shorthand
console.log(!!"hello");      // true
```

```javascript
// A practical use: default values with ||, and its safer cousin ??
function greet(name) {
  const displayName = name || "stranger"; // falls back on ANY falsy value
  return `Hello, ${displayName}!`;
}

console.log(greet(""));    // "Hello, stranger!"  — "" is falsy, so it falls back
console.log(greet(0));     // "Hello, stranger!"  — 0 is falsy too — often NOT what you want!
console.log(greet("Thor")); // "Hello, Thor!"

// The nullish coalescing operator ?? only falls back on null/undefined,
// not on every falsy value — often the safer choice for defaults:
function greetSafer(name) {
  const displayName = name ?? "stranger";
  return `Hello, ${displayName}!`;
}
console.log(greetSafer(0)); // "Hello, 0!" — 0 is a valid value here, so it's kept
```

---

## 🚀 Real World Applications

- **Guard clauses**: `if (!user) return;` is a fast, idiomatic way to bail out early when a value might be `null`, `undefined`, or otherwise missing.
- **Default value fallbacks**: `const port = config.port || 3000;` — though `??` is often the safer choice when `0` or `""` are legitimate values.
- **Conditional rendering** in UI frameworks: `{items.length && <List items={items} />}` (with the well-known gotcha that this can accidentally render a literal `0`).
- **Validating API responses**: checking `if (response.data)` before using it, to guard against `null`/`undefined`/empty responses in one shot.
- **Array/string emptiness checks**: `if (!array.length)` is a common (if slightly indirect) idiom for "is this array empty," relying on `0` being falsy.

---

## ⚖️ Advantages

- Enables extremely concise, idiomatic conditionals without manually writing out comparisons to `null`, `undefined`, etc.
- The falsy list is small and fixed — once memorized, there's no ambiguity left to reason about.
- Plays nicely with short-circuit evaluation (`&&`, `||`), enabling compact patterns like default values and conditional execution.

---

## ⚠️ Limitations

- The list is a fixed part of the language and can't be customized — you can't make a specific object or string "act falsy" without explicitly converting or checking it yourself.
- Truthy checks blur useful distinctions — `if (!value)` treats `0`, `""`, `null`, `undefined`, and `NaN` all identically, even when your logic actually needs to tell them apart (e.g. "no items entered" vs "zero items entered" are very different in a shopping cart).
- Relying too heavily on implicit coercion can make code harder to read for anyone not fluent in the falsy list yet.

---

## 🚨 Common Mistakes

- Using `array.length && <Component />` in JSX and having a literal `0` render on screen when the array is empty, because `0` is falsy but still a renderable value — the fix is `array.length > 0 && <Component />` or `Boolean(array.length) && <Component />`.
- Using `||` for a default value when `0`, `""`, or `false` are legitimate inputs — e.g. `count || 10` silently replacing a real `count` of `0` with `10`. Use `??` (nullish coalescing) instead when only `null`/`undefined` should trigger the fallback.
- Assuming `"0"` or `[]` are falsy because they "look empty" — both are truthy, and this trips up beginners constantly.
- Writing `if (value == false)` to check falsiness instead of `if (!value)` — `==` invokes its own separate coercion rules and doesn't reliably match the truthy/falsy list (e.g. `"0" == false` is actually `true`, but `[] == false` is also `true`, while `if([])` is truthy — these are two different systems that only sometimes agree).
- Forgetting that `NaN` is falsy while still being a `"number"` type — code that checks `typeof value === "number"` before an `if (value)` check can still be silently skipped by a stray `NaN`.

---

## 📖 Further Reading

- MDN: "Truthy"
- MDN: "Falsy"
- *You Don't Know JS: Types & Grammar* (Kyle Simpson) — covers coercion and truthiness in depth

---

## 💡 Wisdom from Mímir

The falsy list is one of the few things in JavaScript I'd genuinely recommend rote-memorizing rather than looking up each time — it's only eight values, it never changes, and knowing it cold means you can read any conditional in any codebase without a flicker of doubt about what it does.

The mistake I see most, even from experienced developers, isn't misremembering the list — it's reaching for `||` as a default-value shortcut without pausing to ask "could a legitimate value here be `0`, `""`, or `false`?" That one habit, more than any coercion trivia, is what actually causes production bugs.

---

## 🔗 Related Notes

- [[== vs === and Type Coercion]] — the sibling concept covering how coercion behaves during comparisons rather than boolean contexts
- [[var vs let vs const]] — another fundamentals note worth pairing with this one when learning JS conditionals
- [[Hoisting]] — a related "know the exact rule, not just the symptom" JavaScript fundamental
