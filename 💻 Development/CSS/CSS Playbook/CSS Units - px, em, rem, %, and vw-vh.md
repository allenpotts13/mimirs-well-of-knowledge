---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: CSS
difficulty: Beginner
tags:
  - units
  - em
  - rem
  - responsive-design
  - fundamentals
aliases:
  - "CSS Units: px, em, rem, %, and vw/vh"
  - "CSS Units: px, em, rem, and vw/vh"
  - em vs rem
  - Relative Units in CSS
  - Viewport Units
publish: true
---

# 📚 CSS Units: px, em, rem, %, and vw/vh

> *CSS gives you both absolute units (fixed, predictable, context-blind) and relative units (scale with something else) — and picking the right one for the right property is what separates a layout that scales gracefully from one that quietly breaks the moment a user changes their font size.*

---

## 🎯 Purpose

`16px` always means 16px. But `1.5em` might mean 24px in one place and 12px somewhere else, depending on what it's relative to. This isn't an inconsistency to work around — it's the whole point. Relative units exist so that spacing, type, and layout can scale proportionally with something meaningful (a parent's font size, the root font size, the viewport) instead of every value needing to be manually recalculated whenever a design's base scale changes.

---

## 🧠 Key Ideas

- `px` is an **absolute** unit — 1px is (roughly) a fixed physical reference regardless of context, so it's predictable but doesn't scale with anything.
- `em` is **relative to the current element's parent font-size** (technically, the inherited font-size it would otherwise compute to) — this makes `em` compound when nested elements each set their own font-size in `em`, since each one multiplies against the one above it.
- `rem` ("root em") is **relative to the root (`<html>`) element's font-size only**, no matter how deeply nested the element is — this sidesteps the `em` compounding problem entirely, which is why `rem` is the more popular default for font sizing today.
- `%` is relative to the **containing block**, but *which* dimension of the containing block depends on the property — `width`/`padding`/`margin` percentages are relative to the containing block's **width** (yes, even `padding-top`/`padding-bottom`), while `height` percentages are relative to the containing block's **height** (and only work if that height is explicitly set, not `auto`).
- `vw` and `vh` are relative to the **viewport** — `1vw` is 1% of the viewport's width, `1vh` is 1% of the viewport's height — independent of any parent element, useful for full-bleed sections and viewport-scaled type.
- A practical convention many teams settle on: `rem` for font sizes and consistent spacing, `px` for things that genuinely shouldn't scale (like a 1px hairline border), `%` for fluid widths within a container, and `vw`/`vh` sparingly for full-viewport-relative sizing.

---

## ⚙️ How It Works

**`px`** is the easy one: it doesn't ask "relative to what," it just *is* a size — a great unit for things that should stay fixed no matter what's around them, like a thin border.

**`em`** asks "relative to my parent's font-size." This is where the classic gotcha lives: if a `<div>` has `font-size: 1.2em` and it's nested inside a `<div>` that *also* has `font-size: 1.2em`, and that one is nested inside yet another `font-size: 1.2em` `<div>` — each layer multiplies against the one before it. Three levels of `1.2em` doesn't give you 1.2× the base size, it gives you 1.2 × 1.2 × 1.2 ≈ 1.73× the base size. Nobody intends this; it just happens silently as components get nested inside other components, especially in component libraries where you don't control what a component might be nested inside of later.

**`rem`** fixes exactly this by always asking "relative to the root," never "relative to my parent." No matter how many `<div>`s deep an element is, `1.5rem` always means 1.5 × the `<html>` element's font-size (16px by default in most browsers, so `1.5rem` = 24px, full stop) — completely immune to nesting depth. This is why `rem` became the default recommendation for font-sizing in modern CSS: it keeps the predictability of `px` while still scaling proportionally if a user (or you) changes the root font size for accessibility or theming.

**`%`** is the trickiest because "relative to the containing block" doesn't mean the same axis every time. Widths and horizontal spacing scale off the container's width, which is intuitive. But vertical padding (`padding-top`/`padding-bottom`) *also* scales off the container's **width**, not height — a deliberate spec decision that trips up nearly everyone the first time they try to build an aspect-ratio box with percentage padding.

**`vw`/`vh`** skip the parent chain entirely and go straight to the browser viewport — `100vh` is always the full visible viewport height, `50vw` is always half the viewport's width, regardless of what element or how deeply nested it is. Handy, but they don't account for scrollbars, mobile browser chrome (address bars appearing/disappearing), or a parent that constrains the element's actual available space.

---

## 💻 Examples

```css
/* px — fixed, absolute, doesn't scale */
.divider {
  border-bottom: 1px solid #ddd; /* a hairline should stay exactly 1px */
}
```

```css
/* em compounding gotcha */
.level-1 { font-size: 1.2em; }   /* 1.2 × 16px = 19.2px */
.level-1 .level-2 { font-size: 1.2em; } /* 1.2 × 19.2px = 23.04px */
.level-1 .level-2 .level-3 { font-size: 1.2em; } /* 1.2 × 23.04px ≈ 27.6px */
/* Each nested level compounds — three "identical" 1.2em rules produce
   three DIFFERENT actual sizes. This is rarely what anyone intended. */
```

```css
/* rem avoids compounding entirely */
html { font-size: 16px; } /* the single source of truth */

.level-1 { font-size: 1.2rem; }              /* 1.2 × 16px = 19.2px */
.level-1 .level-2 { font-size: 1.2rem; }     /* still 1.2 × 16px = 19.2px */
.level-1 .level-2 .level-3 { font-size: 1.2rem; } /* still 19.2px */
/* Nesting depth is irrelevant — every rem value always traces back to
   the SAME root font-size */
```

```css
/* % — relative to the containing block, axis depends on the property */
.container { width: 600px; }

.child {
  width: 50%;         /* 300px — relative to container's WIDTH */
  padding-top: 10%;   /* 60px  — ALSO relative to container's WIDTH,
                          not height, even though it's "padding-top" */
}
```

```css
/* The classic percentage-padding aspect-ratio trick, exploiting that
   vertical padding is width-relative */
.aspect-box {
  width: 100%;
  padding-top: 56.25%; /* 16:9 aspect ratio (9/16 = 0.5625) */
  position: relative;
}
/* Modern CSS has aspect-ratio for this now, but this trick explains
   WHY the old technique worked at all */
```

```css
/* vw/vh — relative to the viewport, not any parent */
.hero {
  height: 100vh;    /* always the full viewport height */
  padding: 5vw;     /* padding scales with viewport width */
}

.responsive-heading {
  font-size: clamp(1.5rem, 4vw, 3rem); /* scales with viewport, but
                                            clamped between sane bounds */
}
```

---

## 🚀 Real World Applications

- Setting `html { font-size: 16px; }` (or leaving the browser default) once, then sizing all typography and spacing in `rem` so a single root change rescales the whole site consistently — including respecting a user's browser-level font-size preference for accessibility.
- Using `%` widths for fluid, container-relative layouts (a sidebar that's always 30% of its parent's width).
- Using `vh` for full-viewport hero sections and `vw` combined with `clamp()` for fluid typography that scales smoothly between a minimum and maximum size.
- Using `px` deliberately for things that should never scale regardless of user or root settings — 1px borders, small fixed icons.
- Avoiding `em` for font-size specifically in deeply nested component trees, while still using `em` intentionally for spacing that *should* scale with a component's own local font-size (e.g. padding on a button, sized in `em` so it grows with the button's text).

---

## ⚖️ Advantages

- `px` gives pixel-perfect predictability when that's genuinely what's needed.
- `rem` gives proportional, accessible scaling without the compounding trap of `em`.
- `%` enables fluid, container-relative layouts without any JavaScript or media queries.
- `vw`/`vh` (especially combined with `clamp()`) enable smooth, continuous scaling of type and spacing across viewport sizes, rather than jumping in steps at breakpoints.

---

## ⚠️ Limitations

- `px` doesn't respect a user's browser font-size accessibility setting — text sized purely in `px` won't grow if a user increases their default font size.
- `em` compounding is a genuine trap in any deeply nested or component-based codebase, and it's easy not to notice until a component ends up nested three levels deep somewhere unexpected.
- `%` requires an explicitly-sized containing block for height percentages to work at all — `height: 50%` inside a parent with `height: auto` simply does nothing.
- `vw`/`vh` don't account for scrollbars (causing minor overflow in some browsers) and can behave unexpectedly on mobile as browser chrome shows/hides during scroll, shifting `100vh` — the newer `dvh`/`svh`/`lvh` units exist specifically to address this.

---

## 🚨 Common Mistakes

- Sizing all typography in `em` inside a component library, then being confused why the same component looks a different size depending on where it's nested.
- Using `rem` for *everything* including things like hairline borders, where a fixed `px` value would be more appropriate and predictable.
- Expecting `height: 100%` to work without realizing every ancestor in the chain also needs an explicit (not `auto`) height for the percentage to resolve against anything.
- Using `100vh` for a full-screen mobile layout and not accounting for mobile browser address-bar show/hide behavior, causing content to be clipped or to jump.
- Mixing `px` and `rem` inconsistently for what should be a single coherent spacing scale, making the whole system harder to reason about and maintain.

---

## 📖 Further Reading

- MDN: "CSS values and units"
- CSS-Tricks: "The EMs have it: proportional media queries ftw!" (classic piece on em/rem reasoning)
- web.dev: "clamp(): the fluid type quandary solved"

---

## 💡 Wisdom from Mímir

My default rule after being bitten by `em` compounding one too many times in a nested component tree: use `rem` for anything font-size or spacing related that should scale with the design's overall type scale, use `px` for things that genuinely should never move (hairline borders, tiny fixed icons), and reach for `em` *only* when I deliberately want a value to scale with a specific element's own local font-size — like padding on a button that should visually grow if the button's text does. Treat `em`'s relativity as a feature you opt into on purpose, not a default you fall into by habit.

---

## 🔗 Related Notes

- [[The Box Model]] — percentage padding's width-relative quirk is a direct extension of box-model math
- [[Responsive Design with Media Queries]] — relative units and media queries are usually designed together for a coherent responsive system
- [[CSS Grid Layout Fundamentals]] — the grid-specific `fr` unit is a related-but-distinct flexible unit worth contrasting with these
- [[Container Queries]] — a newer complement to viewport units, for sizing relative to a container instead of the viewport
