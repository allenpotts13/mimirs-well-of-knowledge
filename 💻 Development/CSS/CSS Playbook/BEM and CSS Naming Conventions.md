---
type: concept
status: active
created: 2026-09-02
updated: 2026-09-02
technology: CSS
difficulty: Intermediate
tags:
  - bem
  - naming-conventions
  - specificity
  - maintainability
  - methodology
aliases:
  - BEM
  - Block Element Modifier
  - CSS Naming Methodology
---

# 📚 BEM and CSS Naming Conventions

> *BEM (Block__Element--Modifier) is a naming pattern that keeps every class at roughly the same, low specificity, trading terser class names for a codebase where nothing ever unexpectedly overrides anything else.*

---

## 🎯 Purpose

CSS has no built-in concept of "this class belongs to that component" — every selector in a stylesheet is potentially global, and as a codebase grows, two unrelated developers can easily both write `.title` or `.header` for completely different components, causing silent collisions. Worse, once one component's styles get slightly more specific than another's (an ID here, a nested selector there), you end up in a specificity arms race where fixing one bug means writing an even more specific selector, which then becomes the next thing that's hard to override. BEM exists to solve both problems at once: it gives every class a name that's unambiguous about which component it belongs to, and it keeps every selector at the same flat specificity (a single class), so nothing needs to out-rank anything else.

---

## 🧠 Key Ideas

- **Block** — a standalone, reusable component: `.card`, `.nav`, `.button`.
- **Element** — a part of a block that has no meaning outside it, written `block__element`: `.card__title`, `.card__image`, `.nav__link`.
- **Modifier** — a variant or state of a block or element, written `block--modifier` or `block__element--modifier`: `.card--featured`, `.button--disabled`, `.card__title--large`.
- Every BEM class is intentionally used **alone**, as a single class selector (`.card__title { }`, never `.card .title { }`) — this is what keeps specificity flat and predictable across an entire codebase.
- Because BEM classes never rely on nesting or descendant selectors for their styling, components become genuinely portable — a `.card__title` looks and behaves the same wherever it's dropped, with no dependency on its surrounding HTML structure.
- BEM doesn't eliminate the need for design discipline — it just makes naming collisions and specificity creep structurally harder to fall into by convention, not by force.

---

## ⚙️ How It Works

Think of BEM class names as a component's full mailing address instead of just a house number. A plain `.title` class is like writing "Title" on an envelope with no street or city — the CSS equivalent of hoping nobody else in the building is also named Title. A BEM name like `.card__title` says "the title, specifically belonging to the card block" — no ambiguity, no accidental delivery to the wrong component.

The specificity discipline is the less obvious but arguably more important half of BEM. Because every BEM selector is a single class (specificity `0-1-0`, in the a-b-c notation), no BEM rule can ever accidentally out-rank another BEM rule through nesting or extra qualifiers. Compare that to a codebase full of `.sidebar .widget h3`, `#main-content .card .title`, and `.card.featured .title` — each of those has a *different* specificity, so predicting which one wins in a given situation requires mental math every time. BEM sidesteps the whole problem: every selector carries the same weight, so the *only* thing that determines which rule wins is source order (last rule wins) — which is far easier to reason about than a specificity table.

The Block/Element/Modifier split also encodes a mental model of component structure directly into the CSS: reading `.card__title--large` tells you, without opening any HTML, that this is a title, that it belongs to a card, and that it's the "large" variant of that title — a level of self-documentation plain class names like `.big-title` simply don't carry.

---

## 💻 Examples

```html
<!-- Block: .card. Elements: .card__image, .card__title, .card__body.
     Modifier: .card--featured changes the whole block's appearance. -->
<article class="card card--featured">
  <img class="card__image" src="thumbnail.jpg" alt="">
  <h3 class="card__title">Article Headline</h3>
  <p class="card__body">A short summary of the article goes here.</p>
  <button class="card__cta card__cta--disabled" disabled>Read more</button>
</article>
```

```css
/* Every rule below is a single class selector — flat specificity (0-1-0)
   across the entire block, no nesting, no ID hacks, no fighting for rank */
.card {
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 1rem;
}

.card__image {
  width: 100%;
  border-radius: 4px 4px 0 0;
}

.card__title {
  font-size: 1.25rem;
  margin: 0.5rem 0;
}

.card__body {
  color: #555;
  line-height: 1.5;
}

/* Modifier on the block itself — a variant of the whole card */
.card--featured {
  border-color: gold;
  box-shadow: 0 0 12px rgba(255, 215, 0, 0.3);
}

/* Modifier on an element — a variant of just that one part */
.card__cta--disabled {
  opacity: 0.5;
  cursor: not-allowed;
}
```

```css
/* What BEM helps you AVOID — a specificity arms race with nested,
   context-dependent, escalating selectors */
.sidebar .card .title { font-size: 1.1rem; }
.sidebar .card.featured .title { font-size: 1.3rem; } /* now fighting the line above */
#main-content .card .title { font-size: 1.4rem; }      /* now fighting both lines above */
/* Three different specificities, three different mental calculations
   just to know which font-size actually applies to a given .title. */
```

---

## 🚀 Real World Applications

- Component libraries and design systems where the same `.card`, `.button`, or `.modal` block gets reused across dozens of pages by different developers, and naming collisions would otherwise be inevitable.
- Large team codebases where CSS is written by many people over years — BEM's self-documenting names make it possible to understand a component's structure from the class names alone, without opening the HTML.
- Legacy CSS audits: recognizing a codebase's specificity problems and introducing BEM (even partially, one component at a time) as a path out of `!important` and ID-selector escalation.
- Style guide / pattern library documentation, where BEM's Block/Element/Modifier structure maps cleanly onto how components are catalogued and demoed in isolation.

---

## ⚖️ Advantages

- Flat, predictable specificity across the whole codebase — the "who wins" question becomes about source order, not selector complexity.
- Self-documenting names make component structure legible without cross-referencing HTML.
- Components become genuinely portable, since styling never depends on ancestor context (no `.sidebar .card` style dependency on where a card happens to sit).
- Reduces naming collisions on large teams by scoping every class to its block by name.

---

## ⚠️ Limitations

- Class names get long and can look visually noisy (`.product-card__add-to-cart-button--loading`), which some developers find unpleasant to write and read.
- BEM is a naming *convention*, not something the browser or CSS enforces — nothing stops a team member from breaking the pattern, and inconsistent adoption undermines the whole benefit.
- Doesn't address every CSS maintainability problem on its own — global styles, third-party CSS, and utility overrides still need separate strategies to coexist cleanly with BEM.
- Deeply nested UI (an element inside an element inside an element) doesn't map cleanly onto BEM's two-level Block/Element structure, forcing awkward compound names or splitting into sub-blocks.

---

## 🚨 Common Mistakes

- Nesting BEM elements in the class name to mirror HTML nesting, e.g. `.card__body__title` — BEM intentionally flattens this to `.card__title`; an element's name reflects its relationship to the *block*, not its DOM depth.
- Combining BEM classes with descendant selectors anyway (`.card .card__title`), which quietly reintroduces the specificity problems BEM exists to prevent.
- Overusing modifiers for things that are really just new blocks — if a "variant" changes so much that it doesn't share most of the base block's styles, it may deserve to be its own block.
- Forgetting the double underscore/double hyphen convention and drifting into inconsistent separators (`.card-title`, `.card_title`, `.cardTitle`) within the same codebase, which reintroduces the ambiguity BEM was meant to remove.
- Applying BEM at the wrong granularity — turning every single `<span>` into a named element even when it has no independent meaning outside its immediate parent adds verbosity without adding clarity.

---

## 📖 Further Reading

- BEM official documentation (bem.info) — "Key Concepts"
- CSS-Tricks: "BEM 101"
- Tailwind CSS documentation — for contrast, the utility-first alternative philosophy

---

## 💡 Wisdom from Mímir

BEM's real gift isn't the naming syntax — it's the discipline it forces of never nesting a selector to win a specificity fight. I've inherited codebases where every bug fix added one more level of nesting or one more `!important`, and untangling them always meant starting over with something BEM-shaped. Worth naming honestly, though: utility-first frameworks like Tailwind solve the exact same underlying problem — specificity chaos in growing codebases — by taking the opposite route, avoiding custom class names (and therefore custom cascade rules) almost entirely in favor of small, single-purpose utility classes composed directly in markup. Both are legitimate answers to "how do I stop CSS from fighting itself"; they just disagree about whether the fix belongs in your class names or in your build tooling.

---

## 🔗 Related Notes

- [[Understanding Specificity]] — the specificity mechanics BEM is deliberately designed to sidestep
- [[The Cascade and Inheritance]] — BEM's flat specificity means source order, governed by the cascade, becomes the primary tiebreaker
- [[CSS Reset vs Normalize]] — often paired with BEM as part of a deliberate, low-specificity foundation for a design system
