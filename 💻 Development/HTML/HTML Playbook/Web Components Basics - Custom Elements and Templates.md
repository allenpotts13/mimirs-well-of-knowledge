---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: HTML
difficulty: Advanced
tags:
  - web-components
  - javascript
  - custom-elements
  - shadow-dom
aliases:
  - Web Components
  - Custom Elements
  - customElements.define
publish: true
---

# 📚 Web Components Basics: Custom Elements and Templates

> *`customElements.define()` lets you invent your own HTML tag, backed by real JavaScript logic and, optionally, its own isolated DOM and styles — a framework-agnostic component model built directly into the browser.*

---

## 🎯 Purpose

Frameworks like React and Vue popularized the idea of "components" — reusable, self-contained chunks of markup, behavior, and style. Web Components exist to provide that same idea as a **native browser feature**, with no framework or build step required. A custom element like `<user-card>` behaves like a real HTML tag: it can be dropped into any page, any framework, or a plain static HTML file, and it just works — because the browser itself, not a JavaScript library, knows what it is.

---

## 🧠 Key Ideas

- **`customElements.define('tag-name', ClassName)`** registers a new HTML tag backed by a JavaScript class that extends `HTMLElement` — from then on, `<tag-name>` anywhere in the document is a live instance of that class.
- Custom element names **must contain a hyphen** (`user-card`, not `usercard`) — this is a deliberate, permanent rule so the browser can always distinguish custom tags from any future standard HTML element.
- **`<template>`** holds inert, parsed-but-not-rendered markup — its contents are not displayed, not executed (scripts inside don't run), and not part of the accessible page until explicitly cloned and inserted into the DOM via JavaScript.
- **Shadow DOM** (attached via `element.attachShadow()`) gives a component its own encapsulated mini-DOM tree, with styles that don't leak out and page styles that (mostly) don't leak in — real style and structure isolation, not a convention.
- Native web components shine as **framework-agnostic, embeddable widgets** (a date picker, a video player, a design-system button meant to work everywhere); for everyday application UI, a framework's own component model (React, Vue, Svelte) is usually the more productive day-to-day choice.

---

## ⚙️ How It Works

A custom element is, at its core, a JavaScript class with special lifecycle callback methods the browser calls automatically at the right moments: `connectedCallback()` runs when the element is inserted into the page (a natural place to build its initial content), `disconnectedCallback()` runs when it's removed (a place to clean up event listeners or timers), and `attributeChangedCallback()` runs when a watched attribute changes value. Registering the class with `customElements.define('my-widget', MyWidget)` is what tells the browser's parser: whenever you see `<my-widget>` in the document — whether it was already there when the page loaded or gets added dynamically later — construct this class and treat it as a real element.

`<template>` solves a different, complementary problem: how do you define a chunk of reusable markup *without* the browser rendering it immediately or running any scripts inside it? Its contents live in a special inert document fragment — parsed for validity but never rendered — until JavaScript explicitly grabs `template.content`, clones it (`.cloneNode(true)`), and appends the clone somewhere live in the page. This is the standard way custom elements build their internal markup efficiently, especially when many instances of the same component need the same starting structure.

Shadow DOM is the piece that provides true encapsulation. Attaching a shadow root to an element (`this.attachShadow({ mode: 'open' })`) creates a separate DOM subtree hanging off that element — CSS written inside the shadow root doesn't affect the rest of the page, and (with some deliberate, controlled exceptions like CSS custom properties) the page's own CSS doesn't reach in either. It's the browser-native equivalent of what CSS-in-JS or scoped-styles tooling in frameworks tries to achieve — except it's real isolation enforced by the browser, not a naming convention.

---

## 💻 Examples

```html
<!-- Defining a simple custom element -->
<script>
  class GreetingCard extends HTMLElement {
    connectedCallback() {
      const name = this.getAttribute('name') || 'friend';
      this.innerHTML = `<p>Hello, ${name}! 👋</p>`;
    }
  }

  customElements.define('greeting-card', GreetingCard);
</script>

<!-- Usable immediately, anywhere, like any other HTML tag -->
<greeting-card name="Mímir"></greeting-card>
```

```html
<!-- Using <template> for reusable inert markup -->
<template id="product-card-template">
  <div class="card">
    <h3 class="card-title"></h3>
    <p class="card-price"></p>
  </div>
</template>

<div id="product-list"></div>

<script>
  const template = document.getElementById('product-card-template');
  const list = document.getElementById('product-list');

  const products = [
    { title: 'Mechanical Keyboard', price: '$89' },
    { title: 'USB-C Hub', price: '$34' },
  ];

  products.forEach(product => {
    const clone = template.content.cloneNode(true);
    clone.querySelector('.card-title').textContent = product.title;
    clone.querySelector('.card-price').textContent = product.price;
    list.appendChild(clone);
  });
  // The <template> content never rendered on its own — only the
  // cloned copies inserted into #product-list actually appear
</script>
```

```html
<!-- A custom element using Shadow DOM for style encapsulation -->
<script>
  class ThemedButton extends HTMLElement {
    connectedCallback() {
      const shadow = this.attachShadow({ mode: 'open' });
      shadow.innerHTML = `
        <style>
          /* This CSS is scoped to this component only —
             it cannot leak out and affect the rest of the page */
          button {
            background: #6d28d9;
            color: white;
            border: none;
            padding: 0.6em 1.2em;
            border-radius: 6px;
            font-size: 1rem;
          }
        </style>
        <button><slot></slot></button>
      `;
    }
  }

  customElements.define('themed-button', ThemedButton);
</script>

<themed-button>Click me</themed-button>
<!-- The page's own <button> styles, if any, don't affect this one,
     and this component's button styles don't leak out either -->
```

---

## 🚀 Real World Applications

- Design systems shared across multiple teams using different frameworks (one team on React, another on Vue) often expose their base components as web components, so both teams consume the exact same underlying implementation.
- Embeddable third-party widgets — a chat bubble, a payment button, a review carousel a company distributes to be dropped into any customer's website — are a natural fit for custom elements, since they need to work regardless of the host page's framework or lack thereof.
- Browser-native elements like `<video>` with custom controls, or map/chart libraries, are often implemented as (or wrapped by) custom elements to bundle behavior and markup together.
- `<template>` underlies how many templating and component libraries efficiently stamp out repeated structure without repeatedly re-parsing the same HTML string.

---

## ⚖️ Advantages

- Works in any context — a plain static HTML page, inside React, inside Vue, inside a CMS's rich text field — since it's a browser feature, not a library dependency.
- No build step or framework runtime required; a `<script>` tag and a `customElements.define()` call is enough.
- Shadow DOM provides genuine style isolation, eliminating a whole class of CSS specificity and naming-collision problems by construction rather than convention (like BEM).
- Long-term stability — a component built with web component standards from years ago still works today, unaffected by framework version churn or breaking API changes.

---

## ⚠️ Limitations

- More boilerplate and lower-level than a modern framework's component syntax — no built-in reactive state, templating language, or diffing; you're managing DOM updates largely by hand (or pulling in a small helper library).
- Passing complex data into a custom element (objects, arrays) is awkward compared to a framework's props system — HTML attributes are strings only, so richer data typically goes through JavaScript properties instead.
- Server-side rendering and SEO for content generated inside Shadow DOM can be trickier than with framework-based SSR solutions that have mature tooling for this.
- Styling a custom element *from the outside* is intentionally restricted by Shadow DOM's encapsulation — this is a feature for isolation but can be a real friction point when a consuming page legitimately needs to theme the component.

---

## 🚨 Common Mistakes

- Forgetting the required hyphen in a custom element's tag name (`customElements.define('card', Card)` throws — it must be `user-card`, `my-card`, etc.).
- Assuming `<template>` content is already "in" the page and trying to query or style it directly with regular DOM methods before it's been cloned and inserted.
- Reaching for a full custom-element-plus-Shadow-DOM setup for simple, app-internal UI that a framework's own lightweight component model would build faster and with less code.
- Not cleaning up event listeners or timers in `disconnectedCallback()`, leaking memory when custom elements are repeatedly added and removed from the page.
- Expecting page-level CSS to style the inside of a Shadow DOM component by default, not realizing encapsulation blocks that unless deliberately exposed (via CSS custom properties or `::part()`).

---

## 📖 Further Reading

- MDN: "Web Components" (overview, Custom Elements, Shadow DOM, and `<template>`)
- MDN: `<template>` element reference
- web.dev: "Custom Elements v1: Reusable Web Components"

---

## 💡 Wisdom from Mímir

I reach for native web components almost exclusively for things that need to leave my codebase — a widget I'm shipping to someone else's site, a design-system primitive meant to outlive any one framework choice. For everything living inside my own app, a framework's component model wins on raw productivity nearly every time; the honest question isn't "are web components good" (they are), it's "does this specific piece of UI need to survive outside the walls of my current framework" — and most UI, if you're honest, doesn't.

---

## 🔗 Related Notes

- [[Semantic HTML and Why It Matters]] — custom elements extend the same "tags carry meaning" idea to your own invented vocabulary
- [[Data Attributes (data-＊)]] — a lighter-weight way to attach custom data to elements, often used alongside or instead of full custom elements
- [[div and span - Generic Containers and When to Avoid Them]] — the generic containers a custom element is often a more meaningful upgrade from
