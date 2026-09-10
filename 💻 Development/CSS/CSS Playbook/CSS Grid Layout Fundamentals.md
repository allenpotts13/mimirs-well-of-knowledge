---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: CSS
difficulty: Intermediate
tags:
  - css-grid
  - layout
  - fundamentals
  - two-dimensional-layout
aliases:
  - CSS Grid
  - display grid
  - Grid Layout
publish: true
---

# <span class="rune">ᛟ</span> CSS Grid Layout Fundamentals

> *CSS Grid is a two-dimensional layout system that lets you define rows and columns at the same time and place items precisely into that structure — the tool flexbox was never designed to be.*

---

## 🎯 Purpose

Some layouts simply aren't "a row" or "a column" — they're a grid: a header spanning the top, a sidebar down the left, a main content area, and a footer, all needing to align with each other in both directions simultaneously. Flexbox can approximate this with nested containers and a lot of careful sizing, but it's fighting its own one-dimensional nature to do it. CSS Grid exists to make true two-dimensional layout a first-class, declarative feature instead of a workaround.

---

## 🧠 Key Ideas

- `grid-template-columns` and `grid-template-rows` define the grid's **tracks** — how many columns/rows exist and how big each one is, all declared on the container up front.
- The **`fr` unit** ("fraction") represents a share of the *leftover* space in the grid, after fixed-size tracks are accounted for — `grid-template-columns: 200px 1fr 1fr` gives a fixed 200px column, then splits the rest evenly between two flexible columns.
- Items can be placed explicitly using `grid-column` / `grid-row` (line-number based, e.g. `grid-column: 1 / 3`) or by naming areas with `grid-template-areas` and assigning children to a named region via `grid-area`.
- Grid is **two-dimensional by design** — you control column sizing and row sizing together in the same declaration, and items can span multiple rows *and* columns at once, which flexbox cannot do natively.
- `gap` (row-gap + column-gap combined) works in grid the same way it does in flexbox, providing clean spacing between tracks without margin hacks.
- Grid and flexbox aren't competitors — they're complementary: grid for the overall page/component skeleton, flexbox for aligning content *within* a single grid cell.

---

## ⚙️ How It Works

Think of CSS Grid like laying down architectural blueprints for a building floor before placing any furniture. You first declare the floor plan on the container: how many columns, how wide each one is, how many rows, how tall each one is. Only *after* that structure exists do you start placing furniture (the grid items) into specific rooms — either by pointing at grid line numbers ("this couch spans from wall 1 to wall 3") or by giving rooms names upfront and telling furniture which named room to go in.

This is the core conceptual difference from flexbox. Flexbox items request space and the container distributes it along one axis reactively, item by item, in the order they appear — it's more like people naturally spacing themselves out in a hallway. Grid is the opposite: you define the full 2D structure declaratively *first*, independent of content, and then drop content into it — including deliberately leaving cells empty, or placing an item out of source order into a specific cell. That upfront, structural nature is exactly why grid handles "align this thing in row 2 with that thing in row 4, across three columns" effortlessly, while flexbox would need serious restructuring to do the same.

---

## 💻 Examples

```css
/* A classic holy-grail layout: header, sidebar, main, footer */
.page {
  display: grid;
  grid-template-columns: 200px 1fr;   /* fixed sidebar, flexible main */
  grid-template-rows: auto 1fr auto;  /* header/footer hug content, middle grows */
  grid-template-areas:
    "header header"
    "sidebar main"
    "footer footer";
  min-height: 100vh;
  gap: 16px;
}

.header  { grid-area: header; }
.sidebar { grid-area: sidebar; }
.main    { grid-area: main; }
.footer  { grid-area: footer; }
```

```html
<div class="page">
  <header class="header">Site Header</header>
  <aside class="sidebar">Nav</aside>
  <main class="main">Content</main>
  <footer class="footer">Footer</footer>
</div>
```

```css
/* fr units — flexible tracks that share leftover space */
.gallery {
  display: grid;
  grid-template-columns: repeat(3, 1fr); /* 3 equal-width flexible columns */
  gap: 12px;
}
```

```css
/* Responsive card grid without a single media query */
.cards {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
  gap: 16px;
}
/* auto-fill + minmax: as many 220px+ columns as fit, each sharing
   remaining space equally — cards reflow automatically as the
   viewport resizes */
```

```css
/* Placing items by line number, spanning multiple tracks */
.featured {
  grid-column: 1 / 3; /* span from column line 1 to column line 3 (2 cols) */
  grid-row: 1 / 3;    /* span two rows too — true 2D spanning */
}
```

---

## 🚀 Real World Applications

- Full page shells (header/sidebar/main/footer) where every region needs to align with the others in both directions.
- Responsive card/image galleries using `repeat(auto-fill, minmax(...))` to reflow column count automatically with zero media queries.
- Dashboard layouts where widgets of varying sizes need to slot into a precise, named layout grid.
- Form layouts where labels and inputs need to align into consistent columns across many rows.
- Magazine-style or asymmetric layouts where some items intentionally span multiple rows/columns (a "featured" article spanning 2x2 while others are 1x1).

---

## ⚖️ Advantages

- True two-dimensional control — rows and columns are sized and aligned together, not approximated through nesting.
- `grid-template-areas` gives you a literal visual ASCII-art map of the layout right in the CSS, which is remarkably easy to read and modify.
- `auto-fill`/`auto-fit` with `minmax()` produces genuinely responsive layouts without writing a single `@media` query.
- Items can overlap or be placed out of DOM order when needed, decoupling visual layout from markup order (used carefully, this is powerful — used carelessly, it hurts accessibility/tab order).

---

## ⚠️ Limitations

- Steeper learning curve than flexbox — line-based placement and `grid-template-areas` both take real practice to get comfortable with.
- Overkill for simple one-dimensional cases (a single row of nav links) where flexbox is simpler and more appropriate.
- Placing items out of source order visually can create a mismatch between visual layout and DOM/tab order, which is a real accessibility concern if overused.
- Older browsers (pre-2017 evergreen versions) lack support, though this is rarely a practical concern today — still worth a quick caniuse check for projects with unusual support requirements.

---

## 🚨 Common Mistakes

- Reaching for nested flexbox to build a full page skeleton when a single `display: grid` with `grid-template-areas` would be clearer and shorter.
- Confusing grid *lines* (the numbered boundaries between tracks, starting at 1) with grid *tracks* (the columns/rows themselves) when writing `grid-column: 1 / 3`.
- Forgetting that `fr` distributes *leftover* space after fixed-size and content-sized tracks are subtracted — not a percentage of the total.
- Misaligning `grid-template-areas` strings (mismatched column counts per row, or a typo'd area name) — the browser silently ignores the malformed area instead of erroring loudly.
- Using `grid-area` names that don't exactly match between `grid-template-areas` and the child rule — a small typo silently drops the item out of the named placement.

---

## 📖 Further Reading

- MDN: "Basic concepts of grid layout"
- CSS-Tricks: "A Complete Guide to Grid"
- web.dev: "Learn CSS: Grid"

---

## 💡 Wisdom from Mímir

My rule of thumb after years of both: if you're laying out a *page* or a *component skeleton* — something with real structure in two directions — reach for grid first. If you're aligning a handful of items in a single row or column — a toolbar, a button group, a card's internal content — reach for flexbox. The two aren't rivals to pick a "winner" between; the best real-world layouts almost always use grid for the skeleton and flexbox for the muscle inside each grid cell.

---

## 🔗 Related Notes

- [[Flexbox Layout Fundamentals]] — the one-dimensional counterpart; grid and flexbox are complementary, not competing, tools
- [[The Box Model]] — grid items still obey normal box-model sizing rules within their assigned track
- [[Responsive Design with Media Queries]] — `auto-fill`/`minmax()` grids often reduce how many media queries a responsive layout needs
- [[CSS Units - px, em, rem, %, and vw-vh]] — the `fr` unit is grid-specific and behaves differently from any of these, worth contrasting directly
