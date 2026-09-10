---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: CSS
difficulty: Intermediate
tags:
  - css-variables
  - custom-properties
  - cascade
  - theming
aliases:
  - Custom Properties
  - CSS Custom Properties
  - var()
publish: true
---

# 📚 CSS Variables (Custom Properties)

> *A CSS custom property (`--name: value;`) is a value stored on an element that cascades and inherits like any other CSS property, and can be read back anywhere below it with `var(--name)` — live, in the browser, at any time.*

---

## 🎯 Purpose

Hardcoding the same color, spacing value, or font size across dozens of rules makes a design system brittle — change the brand color and you're find-and-replacing across the whole stylesheet. Preprocessors like Sass solved this years ago with `$variables`, but those are a build-time trick: they get compiled away into plain values before the browser ever sees them. CSS custom properties solve the same problem natively, in the browser, at runtime — which unlocks things a compiled variable never could, like changing a value with JavaScript or overriding it differently for one component without recompiling anything.

---

## 🧠 Key Ideas

- Declare a custom property with two leading dashes: `--main-color: #3498db;` — the name is arbitrary but must start with `--`.
- Read it with the `var()` function: `color: var(--main-color);`.
- `var()` accepts an optional second argument as a **fallback**: `var(--main-color, blue)` uses `blue` if `--main-color` isn't defined (or is invalid).
- Custom properties **cascade and inherit** just like `color` or `font-size` — declare one on `:root` for a global value, or on a specific class to override it just for that subtree.
- Unlike Sass variables, custom properties are **resolved live in the browser** — they can be changed at runtime, respond to media queries, and be read or written from JavaScript with `element.style.setProperty('--name', value)` / `getComputedStyle(element).getPropertyValue('--name')`.
- Custom properties are **case-sensitive** and their raw value is essentially just a string until something tries to use it as an actual CSS value — this is why they can even store unitless numbers or partial values used by `calc()`.

---

## ⚙️ How It Works

The mental model that trips up people coming from Sass: a Sass `$variable` is like a **find-and-replace macro** — the Sass compiler substitutes the literal value everywhere the variable is used, and by the time the CSS reaches the browser, the variable is gone completely, replaced with plain text. It has no concept of cascade, inheritance, or runtime — it's a authoring-time convenience only.

A CSS custom property is a **real, living property** sitting on an element, exactly like `color` or `margin`. Because it's a real property, it follows the exact same cascade and inheritance rules covered in [[The Cascade and Inheritance]]: a `--spacing` set on `:root` is inherited by every element on the page unless something further down the tree redeclares it, at which point everything inside *that* subtree sees the new value. This is what makes theming so natural — set `--brand-color: navy;` on `.dark-theme` and every `var(--brand-color)` used anywhere inside `.dark-theme` picks up `navy` automatically, no re-render logic required.

And because the browser keeps the variable alive rather than erasing it at compile time, JavaScript can reach in and change it on the fly — `document.documentElement.style.setProperty('--brand-color', 'crimson')` instantly repaints every single `var(--brand-color)` usage on the page, live, with zero extra CSS rules. A Sass variable could never do that; once compiled, it's just a hardcoded value sitting in a stylesheet.

---

## 💻 Examples

```css
/* Declare custom properties globally on :root, the conventional place for theme values */
:root {
  --brand-color: #3498db;
  --spacing-unit: 8px;
  --font-body: 'Helvetica Neue', sans-serif;
}

body {
  font-family: var(--font-body);
}

.button {
  background: var(--brand-color);
  padding: calc(var(--spacing-unit) * 2); /* custom properties work inside calc() */
}
```

```css
/* Fallback value: used only if --accent-color is undefined or invalid */
.badge {
  background: var(--accent-color, gray);
}
```

```css
/* Overriding per-component — this is where custom properties really shine.
   Anything inside .dark-theme sees the new value; nothing outside does. */
:root {
  --bg: white;
  --text: black;
}

.dark-theme {
  --bg: #1a1a1a;
  --text: #f0f0f0;
}

.card {
  background: var(--bg);
  color: var(--text);
}
```

```js
// Reading and writing custom properties from JavaScript — impossible with a compiled Sass variable
const root = document.documentElement;

// Read the current value (returns a string, e.g. "#3498db")
const brandColor = getComputedStyle(root).getPropertyValue('--brand-color');

// Write a new value — every var(--brand-color) usage repaints immediately
root.style.setProperty('--brand-color', '#e74c3c');
```

```css
/* Custom properties can even change per breakpoint via media queries —
   another thing a build-time Sass variable can't do on its own */
:root {
  --container-width: 90%;
}

@media (min-width: 1024px) {
  :root {
    --container-width: 960px;
  }
}

.container {
  width: var(--container-width);
}
```

---

## 🚀 Real World Applications

- Building a light/dark theme toggle by swapping a handful of `:root` custom properties instead of duplicating entire stylesheets.
- Centralizing a design system's spacing scale, color palette, and breakpoints so a single change ripples everywhere consistently.
- Letting JavaScript-driven components (a color picker, a font-size slider, a theming UI) update styles live without injecting new `<style>` tags or inline styles all over the DOM.
- Overriding a single design-system value for one specific component instance, e.g. `<div class="card" style="--card-padding: 4px;">`, without writing a whole new CSS class.
- Powering animations/transitions where a JS-driven value (like a drag position or a computed progress percentage) needs to feed into CSS.

---

## ⚖️ Advantages

- Live in the browser — readable and writable from JavaScript, unlike compiled preprocessor variables.
- Follow the cascade, so they can be scoped, overridden, and inherited exactly like normal CSS properties, enabling clean component-level theming.
- Work naturally with media queries and other runtime conditions, since they're resolved at render/paint time, not compile time.
- No build step required at all — usable directly in plain CSS with zero tooling.
- Can store arbitrary values (including unitless numbers for use in `calc()`), giving them more flexibility than people expect at first.

---

## ⚠️ Limitations

- Can't be used inside media query conditions themselves (`@media (min-width: var(--bp))` is invalid) — they only work as property *values*.
- Slightly more verbose to type than a Sass `$variable`, and there's no built-in "variable doesn't exist" compile-time error — an invalid or undefined one silently falls back or does nothing.
- No built-in type checking or math operations of their own — you still need `calc()` to do arithmetic with them.
- Older browsers (pre-2017 or so) don't support them at all, though this is now essentially a non-issue for modern projects.
- Because they inherit, an unexpected override higher up the tree can silently change a value deep in a component in a way that's harder to trace than a plain hardcoded value.

---

## 🚨 Common Mistakes

- Forgetting the double-dash prefix (`--`) when declaring or referencing a custom property — `color: var(main-color);` is simply invalid.
- Expecting `var()` without a fallback to gracefully do nothing when undefined — instead the property using it is treated as having its **initial** value, which can look like a bug.
- Trying to use a custom property inside a media query condition or a selector, where `var()` isn't allowed at all.
- Declaring the same custom property at multiple levels and being confused about which one "wins" — remember it's just normal cascade/specificity/inheritance rules, same as any property.
- Assuming custom properties behave like Sass variables and get "erased" — forgetting they're inspectable and overridable live in DevTools, which is actually a debugging superpower once you remember it's there.

---

## 📖 Further Reading

- MDN: "Using CSS custom properties (variables)"
- MDN: "CSSStyleDeclaration.setProperty()"
- web.dev: "CSS custom properties"

---

## 💡 Wisdom from Mímir

The moment CSS variables really "clicked" for me was realizing they aren't a scoped-down version of Sass variables — they're a fundamentally different tool that happens to look similar. Sass variables are a text-substitution trick that dies at build time; CSS custom properties are living values sitting in the cascade, inspectable and editable right in DevTools while the page is running. If you only ever use them the way you used Sass variables (one global set on `:root`, never touched again), you're leaving the best part on the table — the real power shows up the moment you start redeclaring one on a class to theme a single component, or reaching into it from JavaScript to drive something the stylesheet alone never could.

---

## 🔗 Related Notes

- [[The Cascade and Inheritance]] — custom properties are ordinary properties for cascade and inheritance purposes, which is the whole reason they can be overridden per-component
- [[CSS Units - px, em, rem, %, and vw-vh]] — custom properties often store unit values and are frequently combined with `calc()`
- [[Responsive Design with Media Queries]] — a common pattern redefines custom properties inside a media query to adjust values per breakpoint
- [[Using !important and When to Avoid It]] — another cascade-related tool worth understanding alongside custom-property overrides
