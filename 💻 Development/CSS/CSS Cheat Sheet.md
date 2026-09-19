---
publish: true
status: published
permalink: css/css-cheat-sheet
---

# <span class="rune">ᛊ</span> CSS Cheat Sheet

> Dense reference across core CSS. Each section links back to the full [[CSS Codex|concept note]] for depth.

---

## Fundamentals

```css
/* Box model — width/height apply to CONTENT ONLY by default */
.box {
    box-sizing: border-box;    /* width/height now include padding + border — set this globally */
    width: 200px;
    padding: 10px;
    border: 1px solid black;
    margin: 20px;               /* OUTSIDE the border — space between elements */
}
```

```text
Cascade order (highest wins): !important > inline style > ID > class/attribute/pseudo-class > element
Specificity is calculated PER SELECTOR, not per rule — (inline, IDs, classes, elements)
```

```css
div p { color: red; }              /* descendant combinator — any p inside a div */
div > p { color: red; }             /* CHILD combinator — only direct children */
h2 + p { color: red; }               /* adjacent sibling — the p immediately after an h2 */
[type="text"] { border: 1px; }        /* attribute selector */
a:hover { color: blue; }               /* pseudo-CLASS — a STATE */
p::first-line { font-weight: bold; }    /* pseudo-ELEMENT — a PART of the element */

16px           /* px  — fixed, absolute */
1.5em          /* em  — relative to the PARENT's font size, COMPOUNDS when nested */
1.5rem         /* rem — relative to the ROOT font size ONLY, does NOT compound — prefer this */
50%            /* %   — relative to the parent */
50vw / 50vh    /* relative to the VIEWPORT width/height */

:root {
    --primary-color: #3498db;    /* custom property — defined once */
}
.button {
    color: var(--primary-color, blue);  /* used anywhere, with an optional fallback */
}
```

See: [[The Box Model]] · [[The Cascade and Inheritance]] · [[Understanding Specificity]] · [[Selectors Beyond the Basics - Combinators and Attribute Selectors]] · [[Pseudo-Classes and Pseudo-Elements]] · [[CSS Units - px, em, rem, %, and vw-vh]] · [[CSS Variables (Custom Properties)]]

---

## Layout

```css
.flex-container {
    display: flex;
    flex-direction: row;            /* row (default) | column */
    justify-content: center;         /* main-axis alignment */
    align-items: center;              /* cross-axis alignment */
    gap: 1rem;
}
.flex-item { flex: 1; }                /* grow/shrink to fill available space */

.grid-container {
    display: grid;
    grid-template-columns: repeat(3, 1fr);  /* 3 equal-width columns */
    grid-template-rows: auto;
    gap: 1rem;
}
.grid-item { grid-column: 1 / 3; }        /* spans columns 1 through 2 */
```

```css
position: static;    /* default — normal document flow, top/left/etc. have NO effect */
position: relative;   /* offset from where it WOULD have been, still takes up its original space */
position: absolute;    /* removed from flow, positioned relative to nearest positioned ancestor */
position: fixed;         /* removed from flow, positioned relative to the VIEWPORT, stays on scroll */
position: sticky;         /* relative until a scroll threshold, then behaves like fixed */

display: block;    /* full width, starts on a new line (div, p, h1) */
display: inline;    /* only as wide as content, no line break, CANNOT set width/height/margin-top/bottom */
display: inline-block; /* inline flow, BUT width/height/margin DO work */
display: none;          /* removed entirely — no space reserved (unlike visibility: hidden) */

z-index: 10;   /* only works on POSITIONED elements (not static) — higher stacks on top */
overflow: hidden;   /* auto | scroll | visible (default) */
```

See: [[Flexbox Layout Fundamentals]] · [[CSS Grid Layout Fundamentals]] · [[Positioning - static, relative, absolute, fixed, and sticky]] · [[The display Property in Depth]] · [[Float and Its Legacy Layout Role]] · [[z-index and Stacking Contexts]] · [[Overflow and Scrolling Behavior]]

---

## Responsive Design

```css
/* Mobile-first — base styles for small screens, THEN override upward */
.card { width: 100%; }

@media (min-width: 768px) {
    .card { width: 50%; }
}
@media (min-width: 1024px) {
    .card { width: 33%; }
}

/* Container queries — respond to the PARENT's size, not the viewport */
.card-container { container-type: inline-size; }
@container (min-width: 400px) {
    .card { font-size: 1.2rem; }
}
```

**Mobile-first** = base styles small, override with `min-width` queries going up.
**Desktop-first** = base styles large, override with `max-width` queries going down.

See: [[Responsive Design with Media Queries]] · [[Container Queries]] · [[Mobile-First vs Desktop-First Design]]

---

## Visual Effects

```css
.button {
    transition: background-color 0.3s ease, transform 0.2s;
}
.button:hover { transform: scale(1.05); }

@keyframes fadeIn {
    from { opacity: 0; }
    to { opacity: 1; }
}
.element { animation: fadeIn 1s ease-in; }

.rotated { transform: rotate(45deg) translateX(10px) scale(1.2); }

@font-face {
    font-family: "CustomFont";
    src: url("font.woff2") format("woff2");
}
body { font-family: "CustomFont", sans-serif; }   /* always provide a fallback */
```

See: [[Transitions and Animations]] · [[The transform Property]] · [[Custom Fonts with @font-face]]

---

## Conventions & Practices

```css
/* BEM — Block__Element--Modifier */
.card { }
.card__title { }
.card__title--highlighted { }
```

```css
/* Centering — the modern, reliable way */
.center-flex {
    display: flex;
    justify-content: center;
    align-items: center;
}
.center-absolute {
    position: absolute;
    top: 50%; left: 50%;
    transform: translate(-50%, -50%);
}
.center-margin {
    width: 200px;
    margin: 0 auto;    /* horizontal centering only, needs a defined width */
}
```

```css
* { margin: 0; padding: 0; box-sizing: border-box; }  /* RESET — strips ALL default styling */
/* Normalize.css instead: makes browser defaults CONSISTENT, doesn't strip them */

.override { color: red !important; }  /* wins regardless of specificity — LAST resort, hard to override later */
```

See: [[BEM and CSS Naming Conventions]] · [[Centering Elements in CSS]] · [[CSS Reset vs Normalize]] · [[Using !important and When to Avoid It]]

---

## Quick Reference

See: [[Common CSS properties]] · [[CSS Colors]]

---

## 🔗 Related Notes

- [[CSS Codex]]
- [[HTML Cheat Sheet]]
