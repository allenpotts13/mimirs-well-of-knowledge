---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: CSS
difficulty: Advanced
tags:
  - z-index
  - stacking-context
  - positioning
  - debugging
aliases:
  - Stacking Context
  - z-index Not Working
publish: true
permalink: css/z-index-and-stacking-contexts
---

# <span class="rune">ᛟ</span> z-index and Stacking Contexts

> *`z-index` only ever competes with sibling elements inside the same stacking context — the reason "`z-index: 999999` isn't working" is almost never a low number, it's an invisible wall an ancestor built that the element can't climb over.*

---

## 🎯 Purpose

Sooner or later a dropdown, modal, or tooltip renders *behind* something it should clearly be in front of, no matter how absurdly high you crank its `z-index`. This concept exists because `z-index` isn't a single global ranking system for the whole page — it only resolves fights *within* a local group called a stacking context, and understanding how those groups form is the only way to actually fix the bug instead of guessing bigger numbers.

---

## 🧠 Key Ideas

- `z-index` has **no effect whatsoever** on an element with `position: static` (the default) — it only does anything on **positioned** elements (`relative`, `absolute`, `fixed`, `sticky`).
- A **stacking context** is a self-contained group of elements that get painted together as a unit; `z-index` values only compare **siblings within the same stacking context** — they can never reach outside it.
- A new stacking context is created by more than just `position` + `z-index` — `opacity` less than 1, `transform`, `filter`, `will-change`, `isolation: isolate`, and several other properties **also** create one, even with no `z-index` set at all.
- Once an ancestor creates a stacking context, **everything inside it is painted as a single unit relative to the outside world** — no `z-index` value on a descendant, however large, can make it appear above an element that sits outside that ancestor's stacking context but ranks above the ancestor itself.
- The root `<html>` element always forms the top-level stacking context, and stacking contexts can nest inside each other, each with its own independent internal ordering.

---

## ⚙️ How It Works

Think of stacking contexts as **sealed boxes stacked on a table**, not a single flat deck of cards. Inside one box, you can shuffle the papers (elements) into any order you like using `z-index` — paper 5 goes above paper 2, no problem. But no matter how you shuffle inside that box, none of those papers can poke out and land on top of a paper sitting in a *different* box that itself was placed higher up on the table. The box's position on the table is decided by *its own* stacking order among sibling boxes — the papers inside have no say in that outer contest at all.

This is exactly why `z-index: 999999` on a deeply nested tooltip can still lose to something with a lowly `z-index: 2` elsewhere on the page: if any ancestor of that tooltip created a stacking context — often completely by accident, via `transform: translateZ(0)` used for a performance hack, or `opacity: 0.99` left over from a fade animation, or a `filter` applied for a hover effect — the tooltip's absurd `z-index` only wins fights *inside that ancestor's box*. The ancestor box itself is what gets compared against the rest of the page, and if the ancestor didn't get a high `z-index` (or has no `z-index` at all, defaulting to stacking in normal document order), the whole box — tooltip included — loses.

The fix is never "make the number bigger." It's finding which ancestor unintentionally created a trapping stacking context, and either removing the property causing it, or moving the element outside that ancestor entirely (e.g. rendering a modal via a "portal" directly under `<body>`, which is precisely why libraries like React offer `createPortal` for this exact problem).

---

## 💻 Examples

```css
/* z-index does NOTHING here — this element is position: static (the default) */
.badge {
  z-index: 999; /* silently ignored */
}
```

```css
/* z-index works once the element is positioned */
.badge {
  position: relative;
  z-index: 999;
}
```

```html
<!-- The classic trap: an ancestor's transform silently creates a stacking context -->
<div class="card" style="transform: translateY(0);">
  <!-- This tooltip is now TRAPPED inside .card's stacking context -->
  <div class="tooltip" style="position: absolute; z-index: 999999;">
    I will NEVER appear above .header below, no matter how high my z-index goes.
  </div>
</div>

<div class="header" style="position: relative; z-index: 2;">
  This wins, because .card's own stacking-context "box" only ranks
  at its default position in the stack — it was never given a z-index.
</div>
```

```css
/* Properties that create a NEW stacking context even without z-index — the usual culprits */
.card {
  transform: translateZ(0);   /* common perf hack, unintended side effect */
  /* opacity: 0.99;           also creates one */
  /* filter: blur(0px);       also creates one */
  /* will-change: transform;  also creates one */
}
```

```css
/* The real fix: don't fight it from inside — escape the trapping ancestor.
   In frameworks, this is what "portals" (e.g. React's createPortal) solve. */
body > .modal-root {
  position: fixed;
  z-index: 1000; /* now competing at the top level, not trapped inside a card */
}
```

```css
/* isolation: isolate deliberately creates a stacking context — useful when
   you WANT to contain z-index fights to one component on purpose */
.widget {
  isolation: isolate;
}
```

---

## 🚀 Real World Applications

- Debugging a dropdown menu, tooltip, or modal that renders behind the page header or another component despite a huge `z-index`.
- Deliberately using `isolation: isolate` to contain a self-contained widget's internal `z-index` fights so they can never leak out and clash with the rest of the page.
- Diagnosing why adding a hover `transform` or fade `opacity` to a card broke a dropdown that used to render correctly above it.
- Rendering modals via a "portal" pattern (in React, Vue, etc.) directly under `<body>`, sidestepping the entire stacking-context nesting problem.
- Reviewing DevTools' "Layers" panel or 3D view to visually inspect which elements form their own stacking contexts on a confusing page.

---

## ⚖️ Advantages

- Stacking contexts, once understood, make z-index behavior fully predictable instead of feeling arbitrary or broken.
- `isolation: isolate` gives you an explicit, intentional way to contain a component's internal layering without side effects like changing its position, opacity, or transform.
- Nesting stacking contexts lets large apps keep local z-index scales (e.g. "0–10 inside this widget") without needing one single global registry of every z-index in the app.

---

## ⚠️ Limitations

- Several stacking-context-creating properties (`transform`, `opacity`, `filter`, `will-change`) are also extremely common performance and animation tools, meaning the trap is easy to create completely by accident.
- There's no CSS-only way to ask "does this element currently have its own stacking context?" — you have to reason it out from the properties applied to every ancestor, or use DevTools.
- Once a stacking context traps a descendant, the only real fixes are structural (move the element in the DOM, remove/relocate the trapping property) — there's no `z-index` value that escapes it.

---

## 🚨 Common Mistakes

- Setting `z-index` on a `position: static` element and being confused when it has zero effect.
- Assuming z-index is one single global scale across the whole page, and reaching for ever-larger numbers (`9999`, `999999`) to "win," when the real problem is an ancestor's stacking context.
- Not realizing that `opacity`, `transform`, `filter`, and `will-change` create stacking contexts too — chasing a z-index bug for an hour without checking those properties on ancestors.
- Forgetting that a stacking-context-trapped element can never escape by raising its own `z-index` — only moving it out of the trapping ancestor (in the DOM, or via a portal) fixes it.
- Applying `transform: translateZ(0)` or similar GPU-acceleration hacks without realizing the side effect of creating a new stacking context that traps everything inside it.

---

## 📖 Further Reading

- MDN: "Stacking context"
- MDN: "z-index"
- MDN: "The stacking context" (CSS Positioned Layout guide)

---

## 💡 Wisdom from Mímir

When a z-index fight doesn't make sense, I stop guessing numbers immediately and open DevTools instead — most browsers can show you which elements form stacking contexts, and it turns a confusing bug into an obvious one in seconds. The mental trap is thinking of z-index as a single global ladder everyone climbs; it isn't — it's a ladder that only exists *inside* whatever box you're standing in, and the box itself is competing on a different ladder one level up. The fastest way to build the right instinct is to remember that `transform`, `opacity`, and `filter` are all secretly `z-index`-context-creators too, not just the properties with "position" in the name.

---

## 🔗 Related Notes

- [[Positioning - static, relative, absolute, fixed, and sticky]] — z-index requires a non-static position value to have any effect at all
- [[The transform Property]] — one of the most common accidental creators of a trapping stacking context
- [[Transitions and Animations]] — animating opacity/transform/filter, exactly the properties that create stacking contexts, makes this bug easy to introduce
- [[Overflow and Scrolling Behavior]] — another property that interacts with layout containment in ways that surprise people the same way stacking contexts do
