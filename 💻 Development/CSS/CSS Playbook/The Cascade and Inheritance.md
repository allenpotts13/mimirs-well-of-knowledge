---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: CSS
difficulty: Beginner
tags:
  - cascade
  - inheritance
  - fundamentals
  - specificity
aliases:
  - CSS Cascade
  - Cascading Style Sheets
  - Inheritance in CSS
publish: true
permalink: css/the-cascade-and-inheritance
---

# <span class="rune">ᛟ</span> The Cascade and Inheritance

> *The cascade decides which conflicting rule wins for a given element; inheritance decides whether a property value flows down to that element's children in the first place — two related but distinct mechanisms that give CSS its name.*

---

## 🎯 Purpose

CSS stands for *Cascading* Style Sheets, and the "cascading" part is doing real work: dozens of style sources (the browser's defaults, the user's own settings, and every stylesheet an author writes) can all claim an opinion about the same element's `color`. Without a defined resolution order, that would be chaos. The cascade exists to make conflict resolution predictable. Inheritance is a separate, complementary mechanism that exists so you don't have to redeclare `font-family` on every single element — some properties naturally "flow down" the DOM tree unless told otherwise.

---

## 🧠 Key Ideas

- The cascade resolves competing declarations using this priority order: **origin and importance → specificity → source order** (with a couple of finer sub-steps, like scoping proximity and layers, in modern CSS).
- **Origin** ranks style sources: user-agent (browser default) styles lose to user styles, which lose to author (your) styles — and `!important` flips that order, making `!important` origins win in reverse.
- **Specificity** is the next tiebreaker within the same origin/importance tier — see [[Understanding Specificity]] for the full scoring system.
- **Source order** is the final tiebreaker — if origin and specificity are tied, the declaration that appears later in the CSS wins.
- **Inheritance is separate from the cascade** — it only applies when an element has *no* declared value for a property, in which case some properties default to inheriting their parent's computed value, and others default to a fixed initial value instead.
- Typography-related properties (`color`, `font-family`, `font-size`, `line-height`, `text-align`, `visibility`) inherit by default; box/layout properties (`margin`, `padding`, `border`, `width`, `height`, `background`) do not — this split is intentional, not arbitrary.
- Four keywords let you explicitly control inheritance on any property: `inherit` (force it to take the parent's value), `initial` (reset to the property's spec-defined default), `unset` (acts like `inherit` for naturally-inheriting properties, `initial` otherwise), and `revert` (roll back to the browser/user-agent stylesheet's value, ignoring author styles).

---

## ⚙️ How It Works

Think of the cascade as a courtroom and inheritance as a family inheritance (fittingly).

**The cascade** is how the browser settles a dispute over one property on one element. For every CSS rule that could apply, the browser asks, in order:

1. *Where did this come from, and how important is it?* `!important` author styles > normal author styles > user styles > browser defaults (and `!important` user-agent styles are a rare, powerful outlier above almost everything).
2. *Within the same tier, which selector is more specific?* This is the ID > class > element tuple comparison.
3. *Still tied? Whichever was written last wins.*

**Inheritance**, by contrast, only kicks in when there's *no rule at all* setting a property on an element. In that gap, the browser checks: is this a property that's designed to inherit? If yes (like `color`), the element quietly takes on its parent's computed value. If no (like `border`), the element falls back to that property's built-in initial value (`border` defaults to `none`) instead of picking up the parent's border. This is why setting `color` on `<body>` cascades visual text color all the way down your page for free, but setting `border` on `<body>` does not put a border around every child element.

The four override keywords let you cut across this default behavior deliberately — e.g., forcing a normally non-inheriting property like `border-color` to inherit anyway with `border-color: inherit`, useful when you want a child to match a dynamically-set parent color without hardcoding it twice.

---

## 💻 Examples

```css
/* Origin + importance example */
p { color: purple; }              /* author style */
p { color: green !important; }    /* author !important — wins over the line above */
```

```css
/* Inheritance in action — no rule needed on the <span> */
article {
  color: #222;
  font-family: Georgia, serif;
}
/* Any <span>, <li>, or <a> inside .article inherits color and font-family
   automatically — no rule targeting them was ever written. */
```

```css
/* Non-inheriting properties don't leak down */
.card {
  border: 2px solid navy;
  padding: 16px;
}
/* Children of .card do NOT get a border or padding — those properties
   default to "none" / "0" on children unless explicitly set. */
```

```css
/* The four override keywords */
.parent {
  color: blue;
}

.child-a { color: inherit; }  /* explicitly takes parent's blue, even if
                                  some other rule would otherwise apply */
.child-b { color: initial; }  /* resets to CSS's built-in default (black) */
.child-c { color: unset; }    /* color inherits by default anyway, so this
                                  behaves the same as "inherit" here */
.child-d { color: revert; }   /* rolls back to the browser's UA stylesheet
                                  value, ignoring author rules entirely */
```

```css
/* Forcing a normally non-inheriting property to inherit */
.themed-box {
  border-color: green;
}
.themed-box .icon {
  border-color: inherit; /* icon's border now matches the box's, without
                             hardcoding "green" a second time */
}
```

---

## 🚀 Real World Applications

- Setting `font-family` and base `color` once on `body` or `html` and letting every element inherit them, instead of repeating font rules everywhere.
- Using `all: unset` on a component to strip both inherited and cascade-applied styles back to a clean baseline before restyling it (common for de-styling a `<button>`).
- Debugging why a deeply nested `<span>` has unexpected text color — 9 times out of 10 it's inheriting from a distant ancestor, not a directly-targeted rule.
- Using `revert` to selectively opt an element back out of a CSS reset/normalize stylesheet, restoring native browser styling for just that one element.
- Understanding why `<a>` tags don't automatically pick up the surrounding text color even though `color` inherits — user-agent stylesheets give links their own `color` declaration, which outranks inheritance.

---

## ⚖️ Advantages

- Inheritance dramatically reduces repetition — typography and text-color rules can live in one place near the root.
- The cascade's fixed priority order means conflicts are always resolvable, never ambiguous, across every browser.
- The override keywords (`inherit`/`initial`/`unset`/`revert`) give precise, explicit escape hatches when the default behavior isn't what you want, without resorting to `!important`.

---

## ⚠️ Limitations

- Inheritance is invisible in the CSS you're looking at — a value can come from three ancestors away, which makes tracing "where did this color come from" harder than tracing a direct rule.
- Not all browsers fully agree on obscure cascade edge cases (like layer ordering in `@layer` or shadow DOM boundaries), though the core origin → specificity → source-order model is standard.
- `revert` support and behavior nuances (especially with `@layer` and shadow-root boundaries) are more recent additions and worth double-checking in older browser support tables.
- Relying too heavily on inheritance for layout-adjacent typography (e.g. `line-height`) can produce compounding surprises in deeply nested components — see the compounding gotcha discussed in [[CSS Units - px, em, rem, %, and vw-vh]].

---

## 🚨 Common Mistakes

- Assuming a property will inherit when it doesn't (e.g. expecting a child to pick up a parent's `border` or `padding` automatically).
- Confusing "the cascade" with "specificity" as if they were the same thing — specificity is just one of the three tiebreakers *inside* the cascade.
- Forgetting that `!important` doesn't just beat specificity, it beats normal source order and origin too, which is exactly why it's so disruptive when overused.
- Not knowing the difference between `initial` (spec default) and `revert` (browser/user-agent default) and using the wrong one, especially on properties where those two differ (like `display` on many elements).
- Writing a global reset like `* { all: unset; }` without realizing it also wipes out useful native behaviors (like list bullets or link underlines) that then need to be manually restored.

---

## 📖 Further Reading

- MDN: "Cascade, specificity, and inheritance"
- MDN: "Inheritance" (CSS key concepts)
- MDN: "initial, inherit, unset, revert" reference pages

---

## 💡 Wisdom from Mímir

The cascade and inheritance get treated as one confusing blob by a lot of learners, but keeping them mentally separate cleans up 90% of "why is this element styled that way" mysteries: first ask "is there a rule targeting this element, and if there are several, which one wins the cascade" — and only if the answer is "no rule at all" do you ask "does this property inherit, and from where." Trace it in that order, every time, and you'll rarely need to guess.

---

## 🔗 Related Notes

- [[Understanding Specificity]] — the tiebreaker mechanism the cascade uses when origin and importance are tied
- [[Using !important and When to Avoid It]] — the override that skips both normal cascade priority and source order
- [[CSS Reset vs Normalize]] — reset stylesheets deliberately manipulate inherited and cascaded defaults across the whole page
- [[CSS Variables (Custom Properties)]] — custom properties inherit by default too, which is what makes theming with them possible
