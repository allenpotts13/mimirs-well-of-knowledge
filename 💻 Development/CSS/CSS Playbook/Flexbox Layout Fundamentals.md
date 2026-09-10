---
type: concept
status: active
created: 2026-09-02
updated: 2026-09-02
technology: CSS
difficulty: Beginner
tags:
  - flexbox
  - layout
  - fundamentals
  - flexible-box
aliases:
  - Flexbox
  - display flex
  - Flexible Box Layout
---

# 📚 Flexbox Layout Fundamentals

> *Flexbox is a one-dimensional layout system that lets a container distribute space among its children along a single axis — row or column — solving alignment and spacing problems that used to require float hacks and magic numbers.*

---

## 🎯 Purpose

Before flexbox, centering something vertically or making a row of items evenly space themselves required either exact pixel math, table layouts, or a pile of float/clearfix hacks that broke the moment content changed size. Flexbox exists to answer a very common real-world question directly: "given this row (or column) of items and the space I have, how should they be sized, spaced, and aligned?"

---

## 🧠 Key Ideas

- Flexbox works along **two axes**: the **main axis** (the direction items flow) and the **cross axis** (perpendicular to it) — which axis is "main" depends entirely on `flex-direction`.
- `flex-direction: row` (the default) makes the main axis horizontal; `flex-direction: column` flips it so the main axis is vertical — and this flip changes what `justify-content` vs `align-items` each control.
- `justify-content` aligns/distributes items along the **main axis** (e.g. spacing a row left-to-right); `align-items` (single line) and `align-content` (multiple wrapped lines) align items along the **cross axis**.
- `flex-grow`, `flex-shrink`, and `flex-basis` control how an individual flex child shares available space — almost always written as the `flex` shorthand, e.g. `flex: 1`.
- `flex: 1` is shorthand for `flex-grow: 1; flex-shrink: 1; flex-basis: 0%` — it tells a child "start from zero and grow to fill an equal share of whatever space is left."
- Flexbox is fundamentally **one-dimensional** — it's excellent at laying out a single row or column, but it doesn't natively align items across both rows and columns simultaneously the way [[CSS Grid Layout Fundamentals]] does.

---

## ⚙️ How It Works

Picture a row of people standing in a hallway (the flex container). `flex-direction` decides which way the hallway runs — normally left-to-right (`row`), but you can rotate the whole hallway to run top-to-bottom (`column`) instead. Whichever way the hallway runs is the **main axis**; the direction across the hallway's width is the **cross axis**.

- `justify-content` answers "how do the people space themselves out *along the hallway*?" — packed at the start, the end, centered, or spread out with gaps (`space-between`, `space-around`, `space-evenly`).
- `align-items` answers "how do they line up *across the hallway's width*?" — all against one wall (`flex-start`), the other wall (`flex-end`), centered (`center`), or stretched to fill the hallway's width (`stretch`, the default).

This is the single most important mental flip in flexbox: rotate `flex-direction` from `row` to `column`, and `justify-content`/`align-items` don't change what they do conceptually ("along the main axis" vs "across the cross axis") — but because the main axis itself rotated, `justify-content` now controls vertical spacing and `align-items` now controls horizontal alignment.

`flex-grow`/`flex-shrink`/`flex-basis` then decide how each individual child behaves when there's extra space or not enough space. `flex-basis` is the child's starting size before growing/shrinking is applied; `flex-grow` is a ratio for how much of the *leftover* space a child claims relative to its siblings; `flex-shrink` is the same idea but for when there isn't enough space and children need to shrink proportionally.

---

## 💻 Examples

```css
/* Basic row layout, evenly spaced, vertically centered */
.toolbar {
  display: flex;
  flex-direction: row;       /* default — main axis is horizontal */
  justify-content: space-between; /* spread along the main axis */
  align-items: center;            /* center along the cross axis */
  gap: 12px;
}
```

```html
<div class="toolbar">
  <span class="logo">Site</span>
  <nav>...</nav>
  <button>Sign in</button>
</div>
```

```css
/* Flipping the axis with flex-direction: column */
.sidebar {
  display: flex;
  flex-direction: column;    /* main axis is now vertical */
  justify-content: flex-start; /* now controls vertical stacking */
  align-items: stretch;        /* now controls horizontal width */
  gap: 8px;
}
```

```css
/* flex: 1 — equal-width columns that share leftover space */
.layout {
  display: flex;
}
.sidebar { flex: 0 0 200px; } /* don't grow, don't shrink, fixed 200px basis */
.main    { flex: 1; }         /* grow to fill all remaining space */
```

```css
/* Three unequal-share columns */
.a { flex: 1; }  /* gets 1 part of the leftover space */
.b { flex: 2; }  /* gets 2 parts — twice as much as .a */
.c { flex: 1; }  /* gets 1 part, same as .a */
```

```css
/* Wrapping + align-content for multi-line flex */
.tags {
  display: flex;
  flex-wrap: wrap;         /* allow items onto multiple lines */
  align-content: flex-start; /* controls spacing between wrapped lines */
  gap: 8px;
}
```

---

## 🚀 Real World Applications

- Navigation bars: logo on the left, links in the middle, a button on the right, all vertically centered with `justify-content: space-between; align-items: center`.
- Card layouts where a footer (with action buttons) needs to stay pinned to the bottom of a variable-height card using `flex-direction: column` plus `margin-top: auto` on the footer.
- Centering literally anything, both axes at once, with just `display: flex; justify-content: center; align-items: center`.
- Equal-height sidebar/main-content layouts, since flex items stretch to match the tallest sibling by default (`align-items: stretch`).
- Responsive tag/chip lists that wrap onto new lines gracefully with `flex-wrap: wrap` and consistent `gap` spacing.

---

## ⚖️ Advantages

- Solves vertical centering — historically one of CSS's most notorious pain points — in two lines.
- Content-driven sizing by default (items size to their content unless told otherwise), which adapts naturally to dynamic text/data.
- `gap` gives clean, consistent spacing between items without manual margin math or "last child, remove margin" hacks.
- Excellent browser support and a relatively small, learnable property set for the value it delivers.

---

## ⚠️ Limitations

- Fundamentally one-dimensional — it does not let you align content across rows *and* columns simultaneously; that's [[CSS Grid Layout Fundamentals]]'s job.
- `flex-wrap`'d items don't align into a strict grid — items on different wrapped lines can end up with inconsistent widths since each line distributes space independently.
- Deeply nested flex containers (flex-in-flex-in-flex) can get genuinely hard to reason about, especially when `flex-shrink` behavior interacts with `min-width`/`min-height` defaults (`auto`, not `0`).
- `flex: 1` on children with different content lengths can produce unequal *visual* results if `flex-basis` interacts unexpectedly with intrinsic content size — worth understanding `flex-basis: 0` vs `auto` explicitly when it matters.

---

## 🚨 Common Mistakes

- Forgetting that `flex-direction: column` swaps what `justify-content` and `align-items` each control, and being confused when centering "stops working" after changing direction.
- Using `flex: 1` on every child and being surprised when a child with a long, unbreakable string (like a URL) refuses to shrink — caused by the default `min-width: auto`, which needs `min-width: 0` (or `overflow: hidden`) to override.
- Reaching for margin hacks to space out flex children instead of the much simpler `gap` property.
- Not setting `flex-wrap: wrap` and then fighting overflow when a row of items doesn't fit on smaller screens.
- Using flexbox to try to build a full 2D page layout (header/sidebar/main/footer aligned in both directions) when CSS Grid is the better-suited tool for that job.

---

## 📖 Further Reading

- MDN: "Basic concepts of flexbox"
- CSS-Tricks: "A Complete Guide to Flexbox"
- MDN: "Aligning items in a flex container"

---

## 💡 Wisdom from Mímir

The fastest way to stop being confused by flexbox is to stop thinking of `justify-content` and `align-items` as "horizontal" and "vertical" — think of them as "along the main axis" and "across the cross axis" instead, full stop. The moment you internalize that `flex-direction` is the thing that decides which way is which, the properties stop feeling arbitrary and start feeling like the same two questions asked every time: how do items space out along the direction they flow, and how do they line up across it.

---

## 🔗 Related Notes

- [[CSS Grid Layout Fundamentals]] — the two-dimensional counterpart; use grid when you need row *and* column control at once
- [[The Box Model]] — flex children opt out of margin collapsing, a direct exception worth knowing when combining the two
- [[Centering Elements in CSS]] — flexbox's `justify-content`/`align-items: center` combo is one of the cleanest centering techniques available
- [[The display Property in Depth]] — `display: flex` is what turns a normal block container into a flex container in the first place
