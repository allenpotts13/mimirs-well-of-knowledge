---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: CSS
difficulty: Advanced
tags:
  - container-queries
  - responsive-design
  - components
  - modern-css
aliases:
  - "@container"
  - container-type
  - Element Queries
publish: true
---

# <span class="rune">ᛟ</span> Container Queries

> *`@container` lets a component respond to the size of the box it's actually sitting in, not the size of the browser window — finally letting the same card component look right whether it's in a wide main column or a cramped sidebar.*

---

## 🎯 Purpose

`@media` queries only ever know one thing: the size of the browser viewport. That's fine for page-level layout decisions ("stack the nav below 768px"), but it falls apart for reusable components. A `.card` component might get dropped into a full-width main column *and* a narrow 250px sidebar on the very same page at the very same viewport width — and a media query has no way to distinguish those two situations, because from the viewport's perspective, nothing changed. Container queries solve exactly this: they let CSS respond to the size of a component's actual container, so the same `.card` can genuinely say "make my layout compact when I'm narrow, and spacious when I'm wide" — regardless of what the browser window is doing.

---

## 🧠 Key Ideas

- `@container` queries a component's **containing element**'s size, not the viewport — this is the core distinction from `@media`.
- Before a container query can be used, an **ancestor** element must opt in by declaring `container-type` (commonly `inline-size`, which tracks just the container's width).
- An optional `container-name` lets you name a specific container to query, useful when there are multiple nested containers and you need to target one unambiguously rather than "the nearest ancestor container."
- Container queries solve the "truly reusable component" problem that media queries structurally cannot — a component with no idea what page or layout region it will be dropped into can still respond correctly to its real rendered context.
- Syntax mirrors media queries closely (`@container (min-width: 400px) { ... }`), which makes the mental model transferable even though what's being measured is fundamentally different.

---

## ⚙️ How It Works

A media query is like a thermostat for the whole building — it only knows the temperature of the building as a whole, and every room reacts to that same reading regardless of whether that particular room happens to be small, sunny, or drafty. A container query is like giving every room its own thermostat — the room reacts to *its own* actual conditions, independent of what the rest of the building is doing.

Concretely, this means a `.card` component styled with `@media` queries can only ever ask "is the browser window wide or narrow?" — it has no way to know that it's currently rendering inside a 260px-wide sidebar rather than a 900px-wide main content area, because the viewport is 1400px wide either way. A container query flips the question: instead of asking about the viewport, it asks "how wide is *my nearest containing element that opted in*?" So the exact same `.card` markup and CSS, dropped into two different layout regions on the same page, can render two genuinely different layouts — compact in the sidebar, spacious in the main column — without any JavaScript, without duplicate component variants, and without any knowledge of the page it's embedded in.

The opt-in step matters: a container query only works if *some* ancestor has declared itself a query container via `container-type`. This is deliberate — the browser needs to know which element's size to measure and to establish a new layout containment boundary at that element, so it can't just default every element to being queryable without a performance and correctness cost. `inline-size` is the most common value — it constrains containment to the inline (usually horizontal) axis, which is enough for the vast majority of "is this container narrow or wide" use cases.

---

## 💻 Examples

```css
/* Step 1: an ancestor opts in as a query container */
.sidebar,
.main-content {
  container-type: inline-size; /* track this element's width */
  container-name: layout-region; /* optional, but useful when nesting */
}
```

```css
/* Step 2: the component queries its container, not the viewport */
.card {
  display: flex;
  flex-direction: column; /* compact, stacked layout by default */
  gap: 0.5rem;
}

/* When the CONTAINER (not the browser window) is at least 400px wide,
   switch the card to a side-by-side layout */
@container layout-region (min-width: 400px) {
  .card {
    flex-direction: row;
    gap: 1rem;
  }

  .card__image {
    width: 40%;
  }
}
```

```html
<!-- Same .card component, two different containers, two different
     rendered results — at the exact same viewport width -->
<div class="sidebar">      <!-- narrow: container width ~250px -->
  <div class="card">…card stacks vertically…</div>
</div>

<div class="main-content"> <!-- wide: container width ~800px -->
  <div class="card">…card lays out horizontally, past the 400px threshold…</div>
</div>
```

```css
/* Container query units — cqw (1% of container width) — let you size
   things fluidly relative to the container instead of the viewport */
.card__title {
  font-size: clamp(1rem, 5cqw, 1.5rem);
}
```

---

## 🚀 Real World Applications

- A card component used in both a dashboard's wide main grid and its narrow filter sidebar, rendering appropriately in each without maintaining two separate components.
- A design system / component library shipped to multiple consuming teams whose page layouts are unknown in advance — container queries let the component "just work" wherever it's placed.
- A widget or embeddable module (a weather widget, a product recommendation card) that needs to adapt correctly regardless of the width of the arbitrary page section it's embedded into.
- Newsletter or CMS "block" systems where the same content block can be dragged into a full-width or half-width column, and needs to visually adapt to whichever it lands in.

---

## ⚖️ Advantages

- Solves a real, previously-unsolvable problem — true component-level responsiveness independent of viewport size.
- Syntax closely mirrors `@media`, keeping the learning curve low for anyone who already understands media queries.
- Reduces the need for JavaScript-based "resize observer" hacks that were the only prior workaround for element-based responsiveness.
- Pairs with container query units (`cqw`, `cqh`, `cqi`, `cqb`) for fluid sizing relative to the container, not just conditional style switching.

---

## ⚠️ Limitations

- Requires explicit ancestor opt-in via `container-type` — you can't container-query an element whose ancestor chain hasn't declared a container, which means retrofitting existing layouts takes some restructuring.
- Establishing a container (`container-type: inline-size` or similar) also establishes a layout/style/size containment context on that element, which has its own layout implications (e.g. it affects how that element's contents can influence its own size) worth understanding before applying broadly.
- Newer than media queries, so older browsers (anything before roughly 2023) don't support it — projects needing to support older browsers still need a media-query or JS fallback strategy.
- Can't query the viewport and a container in the exact same rule — you choose one axis of "what am I responding to" per query, which sometimes means combining both `@media` and `@container` deliberately for full coverage.

---

## 🚨 Common Mistakes

- Writing a `@container` query with no ancestor ever declaring `container-type` — the query silently does nothing, which can look like a typo bug rather than a missing setup step.
- Assuming `container-type: inline-size` measures the same thing `@media` does — it measures the *container's* width, and forgetting this leads to confusion when nested containers don't behave like nested viewports.
- Using an ambiguous, unnamed container in deeply nested layouts, where "the nearest container" isn't the one actually intended — this is exactly what `container-name` is for.
- Reaching for JavaScript resize-observer solutions out of habit without checking whether container queries now cover the use case natively.
- Forgetting that establishing a container changes containment behavior for that element, which can have subtle side effects on how its children's sizing (like percentage heights) resolves.

---

## 📖 Further Reading

- MDN: "CSS container queries"
- web.dev: "New responsive design with container queries"
- MDN: "container-type" and "container-name"

---

## 💡 Wisdom from Mímir

For years the honest answer to "can a component know its own rendered width" was "not really, not without JavaScript" — container queries are one of the few CSS features I'd call a genuine, previously-impossible capability rather than a nicer syntax for something we could already do. The mental shift that trips people up isn't the `@container` syntax itself — it's remembering that the *ancestor* has to opt in first. I always add `container-type: inline-size` to a component's direct wrapper as a matter of habit now, the same reflexive way I add `box-sizing: border-box` — cheap insurance for a responsiveness question I'll probably need to ask later anyway.

---

## 🔗 Related Notes

- [[Responsive Design with Media Queries]] — the viewport-based counterpart container queries complement, not replace
- [[CSS Units - px, em, rem, %, and vw-vh]] — container query units (`cqw`, `cqh`) extend this same family of relative sizing units
- [[Flexbox Layout Fundamentals]] and [[CSS Grid Layout Fundamentals]] — the layout systems most commonly reconfigured inside a `@container` block
