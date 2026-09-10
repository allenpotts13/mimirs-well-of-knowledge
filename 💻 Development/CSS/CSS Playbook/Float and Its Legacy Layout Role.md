---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: CSS
difficulty: Intermediate
tags:
  - float
  - layout
  - legacy
  - clearfix
aliases:
  - float
  - clearfix
  - Clearing Floats
publish: true
permalink: css/float-and-its-legacy-layout-role
---

# <span class="rune">ᛟ</span> Float and Its Legacy Layout Role

> *`float` was designed to let text wrap around an image like a magazine column, and for about fifteen years the CSS community bent that one narrow trick into an entire layout system — with predictably leaky results.*

---

## 🎯 Purpose

Before Flexbox (2012+) and Grid (2017+) were usable in real projects, CSS had no proper layout system at all. `float`, originally built so a caption could wrap around a picture like a newspaper clipping, was the only tool available that could pull an element out of normal flow and let something else sit beside it. So an entire generation of developers repurposed it to build multi-column layouts, sidebars, and grids — a workaround that stuck around long enough to become "how CSS layout works" for the better part of two decades. Understanding `float` still matters today: you'll meet it in older codebases, tutorials, and Stack Overflow answers from the 2010s, and its original, legitimate use (text wrapping around an image) is still genuinely the best tool for that one job.

---

## 🧠 Key Ideas

- `float: left` / `float: right` takes an element out of normal document flow and shoves it to one side, letting inline content (text) wrap around its remaining box.
- A floated element still affects layout — it's not the same as `position: absolute`, which removes an element from flow entirely and lets other content ignore it completely.
- If **every child** of a container is floated, the container has nothing left in normal flow to measure its own height against — it collapses to zero height ("the collapsed parent" problem).
- The **clearfix** hack fixes the collapsed parent by forcing the container to acknowledge the floats, most commonly via a pseudo-element with `clear: both`.
- `float` was never a real layout system — it was a text-wrapping property stretched far past its design, which is exactly why it's clunky for grids/columns and why Flexbox and Grid replaced it almost entirely.

---

## ⚙️ How It Works

Think of `float` like a newspaper's photo placement: drop an image into a column of text with `float: left`, and the paragraph text flows around its right and bottom edges, hugging the image's box. That's it — that's the entire original job of `float`. It doesn't create columns, it doesn't balance heights, it doesn't distribute space. It just pulls one element to a side and lets *inline* content wrap around it.

The old "float-based layout" trick abused this: if you floated a `.sidebar` left and a `.main-content` left right after it, both boxes would sit side-by-side instead of stacking — because neither one was "in the way" of the other anymore in the way normal block elements are. That gave you columns, sort of. But because floated elements are only *partially* out of flow, their parent container doesn't grow to contain them. A `<div>` wrapping three floated `.column` children will report a height of `0px`, because as far as height calculation is concerned, the parent has no in-flow children at all — the floats are invisible to it. This is the infamous "collapsed parent."

The **clearfix** fixes this by inserting an invisible, empty pseudo-element after the floated children with `clear: both`, which forces something back into normal flow that the parent *does* have to measure. Once that pseudo-element exists and is cleared, the parent's height calculation includes it, and the parent "contains" its floats again.

---

## 💻 Examples

```css
/* The one job float is still great at: wrapping text around an image */
.article img.pull-left {
  float: left;
  width: 200px;
  margin: 0 1.5rem 1rem 0; /* breathing room between image and wrapped text */
}
```
```html
<article class="article">
  <img class="pull-left" src="portrait.jpg" alt="Author portrait">
  <p>
    This paragraph will wrap neatly around the right and bottom edges
    of the floated image, exactly the way text wraps around a photo
    in a printed magazine column. This is float doing its actual job.
  </p>
</article>
```

```css
/* The old (legacy) float-based two-column layout trick */
.sidebar {
  float: left;
  width: 25%;
}
.main-content {
  float: left;
  width: 75%;
}
/* Problem: the parent wrapping these two has zero height now */
```

```css
/* The classic clearfix hack that fixes the collapsed parent */
.clearfix::after {
  content: "";
  display: block;
  clear: both;
}
```
```html
<div class="layout-wrapper clearfix">
  <div class="sidebar">Sidebar content</div>
  <div class="main-content">Main content</div>
</div>
<!-- Without .clearfix, layout-wrapper's height collapses to 0,
     and any background-color or border on it would be invisible. -->
```

```css
/* Modern alternative to clearfix when you must still support floats */
.layout-wrapper {
  display: flow-root; /* establishes a new block formatting context,
                          which also happens to contain floats — no
                          pseudo-element hack required */
}
```

---

## 🚀 Real World Applications

- Wrapping body text around a pull-quote image or portrait in a blog post or article layout — float's actual, original, still-legitimate use case.
- Maintaining or debugging a pre-2015 codebase that uses float-based grid systems (early Bootstrap, Foundation, and countless hand-rolled layouts all did this).
- Reading older tutorials, books, or Stack Overflow answers that reference "clearfix" — recognizing it as a historical workaround rather than something to reach for in new code.
- Occasionally floating a small badge or icon next to wrapping text inside a card component, where a full flex container would be overkill for that one text-wrap effect.

---

## ⚖️ Advantages

- Still the simplest, most correct tool for the one thing it was actually designed for: text wrapping around an inline image.
- Extremely well-supported — works identically in every browser back to the earliest CSS implementations, with zero prefixing or fallback concerns.
- Lightweight mentally once you're only using it for its real purpose — no clearfix or collapsed-parent gymnastics needed if you're not trying to build a grid with it.

---

## ⚠️ Limitations

- Was never designed as a layout system, so anything beyond "wrap text around one element" requires hacks (clearfix, explicit widths, negative margins for gutters).
- Floated elements are only partially removed from flow, creating the confusing "half in, half out" behavior that causes collapsed parents and unexpected wrapping of unrelated content.
- No concept of equal-height columns, gap spacing, or alignment — everything Flexbox and Grid give you for free had to be manually faked with floats (fixed widths, margin math, clearfixes).
- Source order is rigid — reordering floated "columns" visually without reordering the HTML is difficult, unlike Flexbox's `order` property.

---

## 🚨 Common Mistakes

- Reaching for `float` to build a modern layout (columns, centering, equal-height cards) instead of Flexbox or Grid, which do the job correctly with far less code.
- Forgetting a clearfix (or `overflow: hidden` / `display: flow-root` on the parent) and being confused why a container with floated children has no visible background or border.
- Confusing `float` with `position: absolute` — floats still influence surrounding inline content and layout; absolutely positioned elements do not.
- Applying `clear: both` in the wrong place (on a sibling instead of via a pseudo-element or parent) and getting unexpected extra whitespace.
- Not realizing that floated elements need an explicit `width` in most cases — an un-widthed float can behave unpredictably depending on its content.

---

## 📖 Further Reading

- MDN: "float"
- MDN: "Guide to Flow layout — In-flow and out-of-flow elements"
- CSS-Tricks: "All About Floats"
- CSS-Tricks: "A New Micro Clearfix Hack" (Nicolas Gallagher's classic clearfix)

---

## 💡 Wisdom from Mímir

If you find yourself writing a clearfix in new code today, stop — that's almost always a sign you actually want Flexbox or Grid, and you've wandered into a solved problem's old, abandoned workaround. I still keep `float` in the toolbox for exactly one job: wrapping paragraph text around an image, the way a magazine does it. For everything else — sidebars, cards, columns, centering — float is a screwdriver being used as a hammer, and the fact that it *can* drive a nail doesn't mean it should.

---

## 🔗 Related Notes

- [[Flexbox Layout Fundamentals]] — the modern, purpose-built replacement for float-based column layouts
- [[CSS Grid Layout Fundamentals]] — the modern, purpose-built replacement for float-based grid layouts
- [[The display Property in Depth]] — `display: flow-root` is the clean modern alternative to a clearfix hack
- [[The Box Model]] — floated elements still obey box-model sizing, which matters when giving them explicit widths
