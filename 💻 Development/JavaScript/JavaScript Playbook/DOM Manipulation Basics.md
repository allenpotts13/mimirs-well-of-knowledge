---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: JavaScript
difficulty: Beginner
tags:
  - dom
  - browser-apis
  - fundamentals
  - security
aliases:
  - DOM
  - Document Object Model
  - querySelector
publish: true
---

# 📚 DOM Manipulation Basics

> *The DOM is the live, in-memory tree representation of a webpage's HTML that JavaScript can read and rewrite, and DOM manipulation is the set of methods used to do that.*

---

## 🎯 Purpose

A webpage's HTML is just the starting point — the DOM (Document Object Model) is what the browser builds from it in memory, and it's the *DOM*, not the original HTML file, that JavaScript actually interacts with. This matters because every dynamic behavior you've ever seen on a website — a button revealing a menu, a form validating itself, a to-do list adding items — is DOM manipulation: JavaScript reaching into that in-memory tree and reading or rewriting nodes. Understanding the DOM is the foundation everything interactive on the web is built on.

---

## 🧠 Key Ideas

- `document.querySelector(selector)` returns the **first** element matching any valid CSS selector; `document.querySelectorAll(selector)` returns **all** matches as a static `NodeList`. Both are modern, flexible, and the default choice for most code.
- `document.getElementById(id)` and `document.getElementsByClassName(className)` are older, narrower APIs — they only match by ID or class respectively — but they're slightly faster than `querySelector` for the exact case they cover, since the browser doesn't need to parse a CSS selector string.
- New elements are built with `document.createElement(tagName)` and inserted into the page with `appendChild()` (adds one node) or the more flexible modern `append()` (accepts multiple nodes and plain strings).
- `element.textContent` sets or reads an element's content as **plain text** — safe, and never interpreted as HTML.
- `element.innerHTML` sets or reads an element's content as **actual HTML**, which the browser parses and renders — powerful, but a real **XSS (cross-site scripting) security risk** if ever set using untrusted or user-supplied input without sanitization.

---

## ⚙️ How It Works

Think of the DOM as a family tree built from your HTML tags — `<html>` is the ancestor, `<body>` is a child of it, and every element nested inside becomes a node with parents, children, and siblings. When you call `document.querySelector('.card')`, you're asking the browser to search that tree and hand you back a live reference to the matching node object — not a string, not a copy, but the actual object the browser is using to render the page. Change a property on it (its text, its style, its attributes) and the page updates immediately, because you're not describing a change — you're directly editing the thing being rendered.

Creating an element with `document.createElement()` builds a new node that exists only in memory, disconnected from the visible page — it doesn't appear anywhere until you explicitly attach it to a node that's already part of the tree, using something like `appendChild()` or `append()`.

---

## 💻 Examples

```javascript
// querySelector vs getElementById
const firstCard = document.querySelector('.card');       // first match, by CSS selector
const allCards = document.querySelectorAll('.card');      // NodeList of all matches
const header = document.getElementById('main-header');    // fastest for a known, unique ID

console.log(allCards.length); // e.g. 5
allCards.forEach(card => console.log(card.textContent)); // NodeList supports forEach
```

```javascript
// Creating and appending a new element
const list = document.querySelector('#todo-list');

const item = document.createElement('li');
item.textContent = 'Buy milk';       // safe: sets it as plain text
item.classList.add('todo-item');      // add a CSS class

list.appendChild(item); // now it's actually part of the visible page

// append() is more flexible — accepts multiple nodes AND plain strings at once
list.append(item, ' (urgent)', document.createElement('li'));
```

```javascript
// textContent (safe) vs innerHTML (XSS risk) — the critical distinction
const box = document.querySelector('#message-box');

const userInput = '<img src="x" onerror="alert(\'hacked!\')">'; // untrusted, from a form

box.textContent = userInput;
// Safe: renders the literal text  <img src="x" onerror="alert('hacked!')">
// on the page — it is NOT interpreted as an actual <img> tag

box.innerHTML = userInput;
// DANGEROUS: the browser parses this as real HTML, creates an actual <img> tag,
// the image fails to load, and the onerror handler EXECUTES the alert.
// This is a textbook XSS vulnerability — never do this with unsanitized input.
```

```javascript
// A safe use of innerHTML: only with content YOU control, never raw user input
const card = document.querySelector('#product-card');
const productName = 'Wireless Mouse'; // hardcoded / from a trusted source, not user input

card.innerHTML = `<h3>${productName}</h3><p class="price">$24.99</p>`;
// Fine here because nothing in this string came from an untrusted source.
// If productName came from a user-editable field instead, this would be unsafe —
// use textContent for that piece, or a sanitizing library like DOMPurify.
```

---

## 🚀 Real World Applications

- Rendering a list of items (search results, comments, products) by looping over data and appending elements to the page.
- Building interactive UI without a framework — toggling classes to show/hide menus, updating text in response to user actions.
- Form validation: reading input values, adding/removing error-message elements based on what the user typed.
- Progressive enhancement: taking server-rendered HTML and attaching dynamic behavior to it after the page loads.
- Any custom widget (a modal, a tooltip, a dropdown) that a UI framework doesn't already provide — built directly with DOM APIs.

---

## ⚖️ Advantages

- No dependencies or build tools required — every method here works in a plain `<script>` tag in any browser.
- Direct and predictable: what you set on an element is exactly what gets rendered, with no abstraction layer in between.
- `querySelector`/`querySelectorAll` let you reuse the same CSS selector skills you already have from styling.

---

## ⚠️ Limitations

- Manually keeping the DOM in sync with changing application data gets unwieldy fast in anything beyond a small page — this is precisely the problem UI frameworks (React, Vue, etc.) exist to solve.
- Frequent, unbatched DOM changes (especially ones that affect layout) can hurt performance by triggering repeated reflows/repaints.
- `querySelectorAll` returns a **static** NodeList — it does not automatically update if matching elements are added or removed later; you'd need to query again.
- `innerHTML` re-parses and re-renders the entire content you assign, destroying and recreating any existing child nodes (and their attached event listeners) even for a small change.

---

## 🚨 Common Mistakes

- Setting `innerHTML` with unsanitized user input — the single most common way beginner (and not-so-beginner) code becomes vulnerable to XSS attacks.
- Assuming `getElementsByClassName` or `querySelectorAll` return a real array — they return array-*like* objects; some methods like `.map()` need `Array.from()` first (though modern `NodeList` does support `forEach`).
- Trying to query an element before the DOM has finished loading (e.g. a `<script>` in the `<head>` running before the `<body>` exists), resulting in `null` and a "cannot read property of null" error.
- Forgetting that `querySelector`/`getElementById` return `null` when nothing matches, then immediately calling a method on the result without checking — causing a runtime crash.
- Rebuilding large chunks of the DOM repeatedly in a loop (e.g. calling `appendChild` inside a loop of thousands of items) instead of batching the work with a `DocumentFragment`, hurting performance.

---

## 📖 Further Reading

- MDN: "Document Object Model (DOM)"
- MDN: "Element.innerHTML" (see its security warnings section specifically)
- MDN: "Document.querySelector()"

---

## 💡 Wisdom from Mímir

The `textContent` vs `innerHTML` choice looks like a style preference until the day it isn't — the first time you see an XSS payload slip through a comment box because someone reached for `innerHTML` out of habit, you stop treating it as a style preference forever. My rule: `textContent` is the default, and `innerHTML` is something I reach for deliberately, only for content I trust completely, never for anything that passed through a user's fingers first.

The other lesson that only comes from experience: a `NodeList` from `querySelectorAll` is a snapshot, not a window. If your list of matching elements changes after the page updates, that variable is already stale — you have to query again.

---

## 🔗 Related Notes

- [[Event Handling and Event Delegation]] — how to make the elements you create and select actually respond to user interaction
- [[JSON - Parsing and Stringifying]] — often the source of data used to build the DOM elements described here
- [[Local Storage vs Session Storage]] — another browser API frequently paired with DOM updates to persist what the user has done
