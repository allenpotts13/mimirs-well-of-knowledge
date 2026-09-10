---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: CSS
difficulty: Beginner
tags:
  - pseudo-classes
  - pseudo-elements
  - selectors
  - fundamentals
aliases:
  - "::before and ::after"
  - Single Colon vs Double Colon
  - CSS Pseudo Selectors
publish: true
---

# <span class="rune">ᛟ</span> Pseudo-Classes and Pseudo-Elements

> *Pseudo-classes (`:hover`) select a real element based on a state it's in; pseudo-elements (`::before`) select a piece of an element that doesn't exist in the DOM at all — one colon vs. two colons marks that difference.*

---

## 🎯 Purpose

Not every useful selection target is a state expressible in HTML, and not every visual piece of an element is a real node. You can't add an `is-hovered` class to every link by hand, and there's no HTML tag for "the first line of this paragraph" or "a little decorative icon before this heading." Pseudo-classes and pseudo-elements exist to let CSS reach these two different kinds of things — states, and virtual sub-parts — without you touching the markup or JavaScript at all.

---

## 🧠 Key Ideas

- **Pseudo-classes** (single colon, `:hover`, `:focus`, `:nth-child()`, `:first-child`, `:checked`, `:disabled`) select an element that **already exists** in the DOM, based on its current **state** or **position**.
- **Pseudo-elements** (double colon, `::before`, `::after`, `::first-line`, `::first-letter`, `::placeholder`) select a **sub-part** of an element that has **no corresponding DOM node** — CSS invents it for you.
- The double-colon syntax (`::before`) was introduced in CSS3 specifically to visually distinguish pseudo-elements from pseudo-classes; older code and some browsers still accept a single colon (`:before`) for backward compatibility, but `::before` is the correct modern syntax.
- `::before` and `::after` **do nothing at all unless they have a `content` property** — even `content: "";` (an empty string) — because that property is what actually triggers the box to be generated.
- Pseudo-classes can be chained (`a:hover:not(.disabled)`) and pseudo-elements generally cannot be chained with each other, and only one pseudo-element is allowed per selector.
- `::before` and `::after` are rendered as if they were the element's first and last children respectively, and they inherit the element's `color` and `font` by default like real children would.

---

## ⚙️ How It Works

Think of it like the difference between describing a **mood** and describing a **prop**.

A pseudo-class describes a mood or circumstance a real actor (element) is currently in — "the button, *while being hovered*" or "the paragraph, *being the third child*." The actor was always there; the selector just catches it in a particular moment or position.

A pseudo-element is more like a prop the director adds to the stage that was never written into the script (the HTML). `::before` and `::after` create a generated box attached to an element, positioned just inside its content, before or after everything else. That box has no tag name, no place in your HTML source, and would not show up if you ran `document.querySelectorAll('*')` — the browser renders it, but it isn't a "real" node the DOM exposes for scripting the way a regular child element is.

Because `::before`/`::after` boxes are generated purely for CSS's benefit, the browser needs to be explicitly told there *is* something to generate — that's the job of `content`. Without it, the pseudo-element selector matches nothing renderable, no matter what other styles you throw at it.

---

## 💻 Examples

```css
/* Pseudo-classes: styling a REAL element based on its STATE */
a:hover {
  color: darkorange;
}

a:visited {
  color: purple;
}

input:focus {
  outline: 2px solid dodgerblue;
}

input:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

/* nth-child targets by position among siblings */
li:nth-child(odd) {
  background: #f4f4f4;
}

li:first-child {
  font-weight: bold;
}
```

```css
/* Pseudo-elements: generating content that ISN'T in the DOM */

/* Decorative icon before every external link — no <span> needed in HTML */
a.external::after {
  content: " ↗";
  color: gray;
}

/* Required little arrow for a custom dropdown, purely visual */
.dropdown::after {
  content: "";
  display: inline-block;
  width: 8px;
  height: 8px;
  border-right: 2px solid black;
  border-bottom: 2px solid black;
  transform: rotate(45deg);
}

/* Drop cap effect on the first letter of an article */
.article::first-letter {
  font-size: 3em;
  float: left;
  line-height: 1;
}

/* Styling just the placeholder text of an input */
input::placeholder {
  color: #999;
  font-style: italic;
}
```

```css
/* The classic "clearfix" hack relies on ::after + content together */
.clearfix::after {
  content: "";
  display: table;
  clear: both;
}
```

```html
<!-- Note: nothing in the HTML corresponds to the arrow or the clearfix box -->
<a href="https://example.com" class="external">Visit site</a>
<div class="clearfix">
  <div style="float: left;">I float</div>
</div>
```

---

## 🚀 Real World Applications

- Adding decorative icons, quote marks, or arrows without cluttering the HTML with empty `<span>`s that exist only for styling.
- Building the classic "clearfix" hack to contain floated children (`.clearfix::after { content: ""; clear: both; }`).
- Styling form states — required-field asterisks via `::after`, focus rings via `:focus`, invalid input styling via `:invalid`.
- Zebra-striping tables or lists with `:nth-child(even)` / `:nth-child(odd)` instead of adding a class to every other row by hand.
- Custom-styling browser-default UI pieces you otherwise can't touch, like `::placeholder` text or `::selection` (highlighted text color).

---

## ⚖️ Advantages

- Keeps purely presentational content (icons, decorative shapes, clearfix hacks) out of the HTML, preserving a clean semantic markup.
- State-based pseudo-classes remove the need for JavaScript to toggle classes for simple interactions like hover and focus.
- Positional pseudo-classes (`:nth-child`, `:first-of-type`, `:last-child`) let you style based on structure without adding a single extra class.
- Pseudo-elements are still just CSS — no extra DOM nodes to bloat the accessibility tree or slow down JS traversal.

---

## ⚠️ Limitations

- `::before`/`::after` content is not selectable/copyable text in the traditional sense and by default is **not** exposed to screen readers in a reliable, consistent way — never put essential content there.
- Only one `::before` and one `::after` per element — you can't stack three generated boxes on the same element.
- Pseudo-elements can't contain real interactive elements (you can't put a real `<button>` inside a `::before`), since it's not a genuine DOM node.
- `:nth-child()` counts **all** sibling types by position, which surprises people expecting it to count only same-tag siblings — that's what `:nth-of-type()` is for instead.
- Some pseudo-classes (like `:has()`) have only recently reached full cross-browser support, so older-browser support should be checked before relying on the newest ones.

---

## 🚨 Common Mistakes

- Writing `.box::before { color: red; }` and wondering why nothing shows up — forgetting the required `content: "";` property.
- Using a single colon for pseudo-elements (`:before` instead of `::before`) out of old habit — it still works in most browsers but isn't the correct modern syntax.
- Expecting `::before`/`::after` generated text to be accessible to screen readers or selectable by users — it's unreliable for that and shouldn't carry essential information.
- Confusing `:nth-child(2)` with `:nth-of-type(2)` when siblings are mixed tag types, getting an unexpected element styled (or none at all).
- Trying to chain two pseudo-elements on one selector (`div::before::after`) — only one pseudo-element is permitted per selector.

---

## 📖 Further Reading

- MDN: "Pseudo-classes and pseudo-elements"
- MDN: "::before" and "::after"
- MDN: "Using the :nth-child() pseudo-class in CSS"

---

## 💡 Wisdom from Mímir

The colon count is a genuinely good mnemonic once it clicks: one colon means "this is a real element, just in a certain state" (`:hover`), two colons means "this doesn't exist until CSS conjures it" (`::before`). When you're debugging why a decorative element won't show up, check `content` first — I've lost more time than I'd like to admit to a missing `content: "";` on an otherwise-perfect `::before` rule. And resist the urge to put anything meaningful in generated content; if a screen-reader user or a "select all + copy" needs it, it belongs in real HTML.

---

## 🔗 Related Notes

- [[Understanding Specificity]] — pseudo-classes count toward specificity the same as a class selector; pseudo-elements count like a type selector
- [[Selectors Beyond the Basics - Combinators and Attribute Selectors]] — pseudo-classes and pseudo-elements are often combined with combinators for precise targeting
- [[The Box Model]] — a generated `::before`/`::after` box follows the exact same box-model rules as a real element
- [[Float and Its Legacy Layout Role]] — the classic `::after` clearfix hack exists specifically to solve a float-related layout problem
