---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: JavaScript
difficulty: Beginner
tags:
  - template-literals
  - strings
  - es6
  - fundamentals
aliases:
  - Template Strings
  - Backtick Strings
  - String Interpolation
publish: true
permalink: javascript/template-literals
---

# <span class="rune">ᛟ</span> Template Literals

> *Template literals are strings wrapped in backticks that let you embed real expressions directly inside the text, instead of stitching pieces together with `+`.*

---

## 🎯 Purpose

Before ES6, building a string with dynamic values inside it meant chaining `+` operators between quoted fragments and variables — tedious to write, easy to get wrong (a missing space, a misplaced quote), and painful to read once more than two or three values were involved. Template literals exist to make building dynamic strings as natural as writing the text itself, with the dynamic parts marked clearly and inline.

---

## 🧠 Key Ideas

- Template literals use **backticks** (`` ` ``) instead of single or double quotes to open and close the string.
- **`${expression}`** embeds any JavaScript expression directly inside the string — a variable, a function call, arithmetic, a ternary, anything that evaluates to a value.
- This replaces the older pattern of string concatenation with `+`, which required manually adding spaces and switching between strings and variables.
- Template literals support **native multi-line strings** — pressing Enter inside the backticks produces an actual line break in the resulting string, no `\n` escape needed.
- **Tagged templates** are an advanced feature: a function placed directly before the backticks (`` tag`Hello ${name}` ``) receives the string pieces and interpolated values separately, letting it process the string in custom ways before returning the final result. Most day-to-day code never needs this, but it's the mechanism behind libraries like styled-components.

---

## ⚙️ How It Works

A template literal is just a string with "slots" cut into it. Anything inside `${ }` is treated as a real JavaScript expression — not a special mini-language, just JavaScript — evaluated at the point the template literal runs, and the result is converted to a string and spliced into that exact position. Everything outside the `${ }` slots is left exactly as written, backticks and all, including actual newlines you type — which is why multi-line strings just work, with no escape sequences required.

Tagged templates take this one step further by handing control of that whole process to a function. Normally, JavaScript evaluates the `${ }` expressions and joins everything into a final string automatically. With a tag function in front of the backticks, JavaScript instead calls that function with the literal text pieces (split around each `${ }`) as one argument and each interpolated value as separate arguments — and *the tag function* decides what to do with them and what to return. This is how a library like styled-components can take what looks like a plain CSS string and actually parse, transform, and inject it as real stylesheet rules.

---

## 💻 Examples

```javascript
// Old way — string concatenation
const name = "Ada";
const age = 36;
const oldWay = "Hello, " + name + "! You are " + age + " years old.";

// New way — template literal with interpolation
const newWay = `Hello, ${name}! You are ${age} years old.`;

console.log(oldWay === newWay); // true — same result, much easier to read/write
```

```javascript
// Any expression works inside ${ } — not just plain variables
const price = 49.999;
const quantity = 3;

console.log(`Total: $${(price * quantity).toFixed(2)}`); // "Total: $149.997" -> "$150.00"
console.log(`Status: ${quantity > 0 ? "In stock" : "Sold out"}`); // "Status: In stock"

function formatName(first, last) {
  return `${last}, ${first}`;
}
console.log(`Contact: ${formatName("Grace", "Hopper")}`); // "Contact: Hopper, Grace"
```

```javascript
// Native multi-line strings — no \n needed
const oldMultiLine = "Line one\nLine two\nLine three";

const newMultiLine = `Line one
Line two
Line three`;

console.log(oldMultiLine === newMultiLine); // true — identical output
```

```javascript
// Template literals nest cleanly, which concatenation makes painful
const items = ["apple", "banana", "cherry"];
const list = `Shopping list:\n${items.map((item) => `- ${item}`).join("\n")}`;
console.log(list);
// Shopping list:
// - apple
// - banana
// - cherry
```

```javascript
// Tagged templates — an advanced feature, shown briefly for awareness
function highlight(strings, ...values) {
  // `strings` = the literal text pieces, `values` = the interpolated results
  return strings.reduce((result, str, i) => {
    const value = values[i] !== undefined ? `[${values[i]}]` : "";
    return result + str + value;
  }, "");
}

const item = "coffee";
const price = 4.5;
console.log(highlight`Order: ${item} costs ${price}`);
// "Order: [coffee] costs [4.5]" — the tag function controlled how values were formatted
// This is the same mechanism styled-components uses to parse CSS from a template literal.
```

---

## 🚀 Real World Applications

- Building dynamic UI text, error messages, and log output where variables need to be embedded into readable sentences.
- Constructing URLs and API request bodies with dynamic path segments or query parameters (`` `/api/users/${userId}` ``).
- Generating multi-line HTML snippets or SQL queries directly in JavaScript without awkward string concatenation across lines.
- CSS-in-JS libraries like styled-components use tagged templates to let developers write what looks like real CSS directly inside JavaScript files.
- GraphQL client libraries (e.g. `gql` tagged templates) use the same tagged-template mechanism to parse query strings written inline in code.

---

## ⚖️ Advantages

- Far more readable than `+` concatenation once more than one or two variables are involved.
- Removes an entire class of small bugs — missing spaces, mismatched quotes — that came from manually gluing string fragments together.
- Native multi-line support removes the need for `\n` escapes or array-`join("\n")` tricks for simple multi-line text.
- Any valid JavaScript expression can be embedded, not just simple variables, which removes the need for pre-computing values before building the string.

---

## ⚠️ Limitations

- Overly long or deeply nested template literals (especially generating HTML) can become harder to read than a dedicated templating approach for large blocks of markup.
- Multi-line template literals preserve *exact* whitespace/indentation from the source code, which can produce unexpectedly indented output if not written carefully.
- Tagged templates, while powerful, add a layer of indirection that can make code harder to follow for anyone unfamiliar with the pattern — worth using deliberately, not casually.

---

## 🚨 Common Mistakes

- Mixing up backticks with regular quotes out of habit — `${name}` inside a single- or double-quoted string does nothing; it's printed literally instead of interpolated.
- Forgetting that expressions inside `${ }` are fully evaluated JavaScript — writing complex, hard-to-read logic inline instead of computing it in a variable beforehand.
- Not realizing multi-line template literals include every whitespace character exactly as typed, leading to unwanted leading spaces when the literal is indented to match surrounding code.
- Assuming tagged templates are needed for basic string building — for everyday interpolation, plain `` `${}` `` template literals (no tag function) are all that's needed.

---

## 📖 Further Reading

- MDN: "Template literals (Template strings)"
- MDN: "Tagged templates"

---

## 💡 Wisdom from Mímir

Template literals are one of those ES6 features that has essentially no downside once you know the syntax — I genuinely can't think of a good reason to write `+` string concatenation in new code today. If you catch yourself writing `"text " + variable + " more text"`, that's your cue to reach for backticks instead.

Tagged templates are worth knowing *exist*, even if you never write one yourself, because you'll eventually run into `styled.div\`...\`` or `` gql`...` `` in a real codebase and wonder what kind of syntax that even is. Recognizing "oh, that's a tagged template" saves you from a confused detour into thinking it's some special language feature rather than a plain function call with unusual-looking arguments.

---

## 🔗 Related Notes

- [[Object Destructuring and the Spread-Rest Operators]] — another ES6 syntax convenience commonly used alongside template literals when formatting dynamic data
- [[The Fetch API for HTTP Requests]] — template literals are the standard way to build dynamic request URLs
