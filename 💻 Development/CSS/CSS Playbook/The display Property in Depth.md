---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: CSS
difficulty: Intermediate
tags:
  - display
  - layout
  - fundamentals
  - block-vs-inline
aliases:
  - display block vs inline
  - display none vs visibility hidden
  - inline-block
publish: true
---

# 📚 The display Property in Depth

> *`display` decides not just how much space an element takes up, but whether it participates in layout at all — and the gap between "invisible" (`visibility: hidden`) and "not there" (`display: none`) is where a lot of layout bugs are born.*

---

## 🎯 Purpose

Every element needs to answer a basic question before any other styling matters: how does it relate to the elements around it — does it start a new line, sit inline with text, or vanish from the page entirely? `display` is that answer. It's arguably the single most foundational layout property in CSS, and its values behave differently enough from each other that assuming they're interchangeable is one of the most common sources of "why isn't my width/height/margin working" confusion for people newer to CSS.

---

## 🧠 Key Ideas

- **`display: block`** — takes up the **full available width** by default, starts on a **new line**, and respects `width`, `height`, and margin/padding on **all four sides**.
- **`display: inline`** — flows **within the surrounding text**, does **not** start a new line, and **ignores `width` and `height` entirely** — also ignores top and bottom `margin` (though left/right margin and all padding still apply, just without pushing surrounding line content away vertically).
- **`display: inline-block`** — the hybrid: flows inline with surrounding content like `inline`, but **respects `width`, `height`, and margin/padding on all sides** like `block`.
- **`display: none`** — removes the element from layout **entirely**, as if it were never in the HTML at all — it takes up zero space, and nothing about it is rendered, including for screen readers by default.
- **`visibility: hidden`** is a completely different property (not a `display` value) that makes an element **invisible while still occupying its normal space** in layout — the crucial contrast with `display: none`.
- **`display: contents`** makes the element itself disappear from the box tree (no box, no box-model effects of its own) while its **children remain and render normally**, as if they'd been promoted up a level — useful for unwrapping a layout wrapper without changing the DOM.

---

## ⚙️ How It Works

Think of `display` as answering "what kind of citizen is this element in the page's layout." A `block` element is like a paragraph in a book — it always starts on its own line, claims the full width of the column, and has real, respected margins pushing other paragraphs away above and below it. An `inline` element is like a single word bolded within a sentence — it just sits in the flow of text, wrapping with everything around it, and trying to give it a `height` makes no sense in that mental model, the same way you can't give one word in a sentence its own "height" separate from the line it's on; that's exactly why `inline` ignores `width`/`height` and vertical margin.

`inline-block` exists because sometimes you want both: something that sits inline with surrounding text or elements (doesn't force a new line) but *also* respects real dimensions and full margin like a block element — a common need for things like nav items or inline badges that need explicit sizing.

`display: none` versus `visibility: hidden` is a distinction worth internalizing precisely because they *look* similar in name but behave completely differently. `display: none` is like tearing a page out of the book — the layout re-flows as if it was never there, no space reserved, nothing announced to assistive tech. `visibility: hidden` is like printing the page in invisible ink — the space is still very much reserved in the book (everything else keeps its position exactly as if the content were visible), it's simply not rendered visually (and by default it's also skipped by assistive tech, though descendants can override this back to `visible`).

`display: contents` is the odd one out: it tells the browser "don't generate a box for *this* element at all — pretend it isn't a wrapper, and just render its children as if they were direct children of my parent." The element still exists in the DOM (for JavaScript, for semantics) but contributes nothing to the box tree — no box-model effects, no background, no border, nothing. This is specifically useful when a wrapper `<div>` needed for markup/semantic reasons is breaking a `display: grid` or `display: flex` layout, because grid/flex items must be *direct* children to participate — `display: contents` "dissolves" the wrapper visually so its children become direct participants instead.

---

## 💻 Examples

```css
/* block: full width, new line, respects all margin/padding/width/height */
.paragraph {
  display: block; /* default for <p>, <div>, <section>, etc. */
  width: 300px;
  height: 100px;
  margin: 20px 0; /* top/bottom margin fully respected */
}
```

```css
/* inline: flows with text, ignores width/height and top/bottom margin */
span.highlight {
  display: inline; /* default for <span>, <a>, <strong>, etc. */
  width: 300px;   /* IGNORED — has no effect */
  height: 100px;  /* IGNORED — has no effect */
  margin-top: 20px; /* IGNORED — has no vertical effect */
  margin-left: 10px; /* respected — horizontal margin still works on inline */
  background: yellow; /* padding/background still render, just don't push lines apart */
}
```

```css
/* inline-block: best of both — flows inline, respects real dimensions */
.nav-item {
  display: inline-block;
  width: 120px;
  height: 40px;
  margin: 0 8px; /* fully respected on all sides */
}
```

```css
/* display: none vs visibility: hidden — the crucial contrast */
.gone {
  display: none; /* removed from layout entirely — takes up ZERO space */
}

.invisible {
  visibility: hidden; /* invisible, but STILL occupies its normal space */
}
```
```html
<div class="row">
  <div class="box">A</div>
  <div class="gone">B (display: none)</div>
  <div class="box">C — slides left to fill B's space, since B reserves nothing</div>
</div>

<div class="row">
  <div class="box">A</div>
  <div class="invisible">B (visibility: hidden)</div>
  <div class="box">C — stays in place, since B's empty space is still reserved</div>
</div>
```

```css
/* display: contents — the element disappears from the box tree,
   but its children remain and become direct participants in the parent's layout */
.grid-wrapper {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
}

.unwanted-wrapper {
  display: contents; /* this div itself contributes NO box; children become grid items directly */
}
```
```html
<div class="grid-wrapper">
  <div class="unwanted-wrapper">
    <!-- Without display: contents on the wrapper above, these two divs would be
         ONE grid item (the wrapper), not two — breaking the intended 3-column grid. -->
    <div>Item 1</div>
    <div>Item 2</div>
  </div>
  <div>Item 3</div>
</div>
```

---

## 🚀 Real World Applications

- Building horizontal navigation menus from `<li>` elements using `inline-block` (or more commonly today, flex — but `inline-block` nav bars are still common in older/simpler codebases).
- Toggling a modal, dropdown, or accordion panel's visibility with `display: none` / a real display value, versus using `visibility: hidden` when you need to preserve layout space (e.g. a placeholder that reserves room for content that will load in later).
- Using `display: contents` to "unwrap" a component library's wrapper `<div>` that's breaking a CSS Grid or Flexbox layout without being able to edit the component's markup directly.
- Hiding content specifically from sighted users while keeping it in the layout flow's accessibility tree differently depending on whether `display: none` (fully hidden from everyone) or a visually-hidden-but-accessible technique is appropriate.
- Styling inline elements like `<a>` or `<strong>` with padding/background (works fine) while knowing not to expect `width`/`height` to do anything until switching to `inline-block`.

---

## ⚖️ Advantages

- Having distinct `block`/`inline`/`inline-block` values gives fine control over exactly how an element participates in surrounding flow.
- `display: none` cleanly and completely removes an element from both the visual layout and (by default) the accessibility tree in one property — no lingering reserved space or announced content.
- `visibility: hidden` is uniquely useful for hiding something while preserving its layout footprint — useful for things like a fixed-height area that shouldn't jump around as content is toggled.
- `display: contents` solves the "wrapper div breaks my grid/flex layout" problem without requiring a markup or JavaScript change.

---

## ⚠️ Limitations

- `inline` elements ignoring `width`/`height`/vertical-margin catches nearly everyone off guard at least once — it's not a bug, but it isn't obvious from the property names alone.
- `display: none` removes an element from the accessibility tree by default, meaning it's genuinely hidden from screen reader users too — not appropriate for "visually hide but keep accessible" patterns, which need a different, more specific technique (an `.sr-only`-style clipping class).
- `display: contents` currently has some known accessibility quirks in certain browsers/assistive-tech combinations (in the past, some browsers stripped an element's semantics along with its box) — worth testing before relying on it for anything accessibility-critical.
- Switching an element's `display` value can silently break other styles that assumed a particular display type — e.g. `float` and certain flex/grid properties behave differently or not at all depending on the current `display` value.

---

## 🚨 Common Mistakes

- Setting `width`/`height` on a `<span>` or other naturally-`inline` element and being confused when it has no effect, instead of switching to `inline-block` or `block`.
- Using `visibility: hidden` when `display: none` was actually wanted (or vice versa) — leading to either unwanted reserved blank space, or an unwanted layout shift when content needs to disappear completely.
- Assuming `display: none` still leaves content accessible to screen readers — it does not, by default.
- Forgetting that toggling `display` via JavaScript for a modal/dropdown means also needing to manage focus and ARIA attributes for accessibility — `display` alone doesn't handle that.
- Applying `display: contents` and being surprised that some styles set directly on that element (background, border, padding) simply vanish, because the element no longer generates a box of its own to apply them to.

---

## 📖 Further Reading

- MDN: "display"
- MDN: "Block and inline layout in normal flow"
- MDN: "Visibility"

---

## 💡 Wisdom from Mímir

The `display: none` vs `visibility: hidden` distinction is a great one to drill into muscle memory early, because the wrong choice produces a bug that's easy to misdiagnose — a layout that "randomly" shifts when content disappears is very often someone reaching for `display: none` when the space needed to stay reserved, or vice versa, a layout with a mysterious persistent gap from `visibility: hidden` when the content should have been fully removed. When in doubt, ask yourself: "if this goes away, should everything around it slide over to fill the gap, or should the gap stay exactly where it was?" That question picks the right property every time.

---

## 🔗 Related Notes

- [[The Box Model]] — display type determines which box-model rules (margin, width, height) actually apply to an element
- [[Flexbox Layout Fundamentals]] and [[CSS Grid Layout Fundamentals]] — both are themselves `display` values (`flex`, `grid`), and `display: contents` is specifically useful for fixing wrapper problems in these layouts
- [[Float and Its Legacy Layout Role]] — float historically interacted with `display` in ways that produced its own set of quirks
- [[Positioning - static, relative, absolute, fixed, and sticky]] — an element's `display` and `position` values interact (e.g. `position: absolute` computes an element's display to block-like regardless of its specified display type)
