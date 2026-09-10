---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-06
technology: JavaScript
difficulty: Intermediate
tags:
  - modules
  - import-export
  - es-modules
  - commonjs
aliases:
  - "The Module System: import and export"
  - ES Modules
  - CommonJS vs ES Modules
publish: true
permalink: javascript/the-module-system-import-and-export
---

# <span class="rune">ᛟ</span> The Module System: import and export

> *A module is a self-contained file that explicitly declares what it shares with the rest of the codebase (`export`) and what it borrows from other files (`import`), instead of dumping everything into one shared global scope.*

---

## 🎯 Purpose

Early JavaScript had no built-in module system at all — every script tag dumped its variables into one shared global scope, and keeping large codebases from colliding required careful naming conventions and discipline rather than actual language support. Modules exist to solve that: each file becomes its own private scope by default, sharing only what it deliberately `export`s, and pulling in only what it explicitly `import`s from elsewhere. This concept exists because JavaScript actually has *two* competing module systems in real-world use today — native ES Modules and Node's older CommonJS — and understanding both, plus why the browser needs a special `<script type="module">` to use the modern one, is essential to working in almost any real JavaScript project.

---

## 🧠 Key Ideas

- A **named export** (`export const x = ...`) shares a specific, named value from a module; it's imported with matching curly braces: `import { x } from "./file.js"`. A file can have **any number** of named exports.
- A **default export** (`export default ...`) marks *one* value per file as "the main thing this module provides"; it's imported without curly braces and can be given **any name** on import: `import whateverNameYouWant from "./file.js"`.
- A single file can mix both: one default export plus any number of named exports, imported together as `import Default, { named1, named2 } from "./file.js"`.
- **ES Modules** (`import`/`export`) are the modern, standardized JavaScript module system, natively supported in current browsers and in Node.js (via `.mjs` files, or `"type": "module"` in `package.json`).
- **CommonJS** (`require(...)` / `module.exports = ...`) is the older module system that Node.js used exclusively for years before ES Modules arrived; it's still extremely common in existing Node codebases and npm packages.
- In the browser, `<script type="module">` is required to use `import`/`export` syntax at all — a plain `<script>` tag doesn't understand that syntax. Module scripts also automatically run in **strict mode**, are **deferred** by default (they don't block HTML parsing and run after the document is parsed), and each module's top-level variables stay private to that module rather than leaking onto `window`.

---

## ⚙️ How It Works

Picture each file as a room with its own locked door. By default, nothing inside that room is visible from any other room — every variable, function, and class declared at the top level of a module stays private to it. `export` is the act of placing specific items in a labeled box just outside the door, available for any other room to come and take (via `import`). Named exports are labeled boxes — you ask for something by its exact label (`{ x }`), though `as` lets you relabel it on the way in. A default export is different: it's the *one* unlabeled package left specifically for whoever imports this room's contents, and the importer gets to name it whatever makes sense to them.

CommonJS works on a similar idea but with older, simpler primitives: `module.exports` is the single object handed out by a file, and `require()` synchronously fetches and runs that file to grab it. ES Modules, by contrast, are statically analyzable (the imports/exports are determined by *reading* the code, not by running it) which is what lets bundlers "tree-shake" away unused exports, and they load asynchronously — one reason browsers need the explicit `type="module"` signal, since it changes how and when the script's code actually executes relative to the rest of the page.

---

## 💻 Examples

```javascript
// mathUtils.js — named exports: any number of them, each individually named
export const PI = 3.14159;

export function square(n) {
  return n * n;
}

export function circleArea(radius) {
  return PI * square(radius);
}
```

```javascript
// app.js — importing named exports: names must match, wrapped in { }
import { PI, square, circleArea } from "./mathUtils.js";

console.log(square(4));        // 16
console.log(circleArea(2));    // 12.56636

// You can rename an import on the way in with `as`:
import { square as sq } from "./mathUtils.js";
console.log(sq(5)); // 25
```

```javascript
// userService.js — a default export: ONE main thing per file
export default class UserService {
  constructor(apiUrl) {
    this.apiUrl = apiUrl;
  }
  getUser(id) {
    return fetch(`${this.apiUrl}/users/${id}`).then((r) => r.json());
  }
}

// A file can mix a default export with named ones too:
export const DEFAULT_API_URL = "https://api.example.com";
```

```javascript
// app.js — importing a default export: no braces, and any name you like
import UserService, { DEFAULT_API_URL } from "./userService.js";

const service = new UserService(DEFAULT_API_URL);
// Note: it could just as easily be `import Whatever from "./userService.js"` —
// the name on import is entirely up to the importing file for a default export.
```

```javascript
// The SAME idea in CommonJS (Node's older, still-common module system)

// mathUtils.js (CommonJS)
function square(n) {
  return n * n;
}
module.exports = { square, PI: 3.14159 };

// app.js (CommonJS)
const { square, PI } = require("./mathUtils.js");
console.log(square(4)); // 16
```

```html
<!-- In the browser, type="module" is required to use import/export at all -->
<script type="module">
  import { circleArea } from "./mathUtils.js";
  console.log(circleArea(3));
</script>

<!-- Without type="module", the browser would throw a SyntaxError on `import` -->
<!-- Module scripts also run deferred automatically and in strict mode by default -->
```

---

## 🚀 Real World Applications

- **Component-based frontend frameworks** (React, Vue, Svelte): every component is typically its own module, `export default`-ing the component and often named-exporting helper types or constants alongside it.
- **Splitting a large codebase into focused files**: utility functions, API clients, and constants each live in their own module instead of one enormous script.
- **npm packages**: virtually every published package defines its public API through `export`/`module.exports`, and modern packages often ship both an ES Module and CommonJS build to support both import styles.
- **Tree-shaking in build tools** (Webpack, Rollup, esbuild, Vite): bundlers analyze static `import`/`export` statements to strip out code that's never actually imported anywhere, shrinking the final bundle.
- **Node.js backend projects**: increasingly using native ES Modules (`"type": "module"` in `package.json`) for new code, while older codebases and many still-popular libraries remain CommonJS, making familiarity with both essential.

---

## ⚖️ Advantages

- Keeps each file's variables private by default, drastically reducing accidental naming collisions compared to old-style global scripts.
- Makes a file's dependencies explicit and readable at a glance — the `import` list at the top of a file *is* its dependency list.
- Enables build-tool optimizations like tree-shaking, since ES Module imports/exports can be statically analyzed without running the code.
- A default export gives a file one obvious "main" thing to import, while named exports still allow multiple related utilities to live alongside it.

---

## ⚠️ Limitations

- Two competing module systems (ES Modules and CommonJS) coexisting in the Node/npm ecosystem creates real interoperability friction — importing a CommonJS-only package from an ES Module file (or vice versa) can require extra configuration or workarounds.
- Default exports can't be split into multiple named things without restructuring the file, and renaming them freely on import (a stated advantage) can also make it harder to grep a codebase for "who imports this."
- Browser-native ES Modules make a separate network request per imported file unless bundled, which is why bundlers remain common in production even though modules work natively.
- Circular imports (module A imports from module B, which imports from module A) are technically supported but can produce confusing partially-initialized values if not structured carefully.

---

## 🚨 Common Mistakes

- Mixing `import`/`export` syntax and `require`/`module.exports` in the same file, which throws a syntax or reference error — a file (or the whole project's `package.json` config) has to commit to one system at a time.
- Forgetting `type="module"` on a `<script>` tag that uses `import`, resulting in `Uncaught SyntaxError: Cannot use import statement outside a module`.
- Assuming a default export needs to match the file name or the exported thing's own name on import — it doesn't; the importer can name it anything, which is flexible but also means it's easy to import the same thing under inconsistent names across a codebase.
- Trying to `import` a CommonJS package's named exports directly when the package only provides a `module.exports` object, running into interop quirks depending on the bundler or Node's own CommonJS-interop rules.
- Forgetting that module scripts defer execution — code relying on being run immediately, before the DOM finishes parsing, may behave differently than an equivalent non-module inline script.

---

## 📖 Further Reading

- MDN: "JavaScript modules"
- Node.js documentation: "Modules: ECMAScript modules" and "Modules: CommonJS modules"
- MDN: "`<script>`: The Script element" (for the `type="module"` attribute specifics)

---

## 💡 Wisdom from Mímir

The cleanest mental shortcut I've found for named vs. default exports: named exports are for a *toolbox* of related things a file provides, while a default export is for when a file *is* one thing — a single class, a single component, a single main function. If you find yourself reaching for a default export on a file that actually exports three equally important, unrelated utilities, that's usually a sign the file's actual shape is a toolbox, not a single "the one thing," and named exports would describe it more honestly.

The CommonJS/ES-Modules divide isn't a relic you can safely ignore either — I still run into `require`-only packages in real projects, and understanding *why* the interop sometimes gets awkward (static analysis vs. dynamic runtime loading) saves a lot of confused debugging versus just copy-pasting a fix from a forum post.

---

## 🔗 Related Notes

- [[Higher-Order Functions]] — commonly one of the "named export" utilities modules are built to share
- [[Promises]] — dynamic `import()` (a Promise-returning function, distinct from the static `import` statement) is a related, lazier way to load modules
- [[var vs let vs const]] — module-level scoping builds on the same block/lexical scoping fundamentals
