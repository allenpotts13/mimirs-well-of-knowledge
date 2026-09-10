---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: CSS
difficulty: Beginner
tags:
  - reset
  - normalize
  - browser-defaults
  - fundamentals
aliases:
  - CSS Reset
  - normalize.css
  - Modern CSS Reset
publish: true
---

# <span class="rune">ᛟ</span> CSS Reset vs Normalize

> *Every browser ships its own opinionated default stylesheet, and before you write a single line of your own CSS you have to decide whether to strip those defaults to zero (reset) or just make them consistent (normalize).*

---

## 🎯 Purpose

Browsers don't render a bare `<h1>` or `<ul>` as an unstyled block of text — they apply a built-in stylesheet (the "user agent stylesheet") that gives headings bold text and margins, lists bullets and indentation, buttons a 3D-ish border, and so on. The problem is that these built-in stylesheets aren't identical across browsers — Chrome, Firefox, and Safari disagree on the exact margin of a `<p>` or the default `line-height` of a `<button>`. Left alone, that means your layout can look subtly (or not-so-subtly) different depending on which browser a visitor uses. Resets and normalize stylesheets both exist to solve this — they just take fundamentally different philosophies about *how much* of the browser's default styling to keep.

---

## 🧠 Key Ideas

- A **reset** (classically, Eric Meyer's Reset CSS) aggressively strips almost all default styling — margins, padding, font sizes, list bullets, borders — down to a nearly blank slate, so every visual property is something *you* deliberately set.
- **normalize.css** takes the opposite philosophy: it keeps useful browser defaults (headings still look like headings, lists still have bullets) but fixes inconsistencies and bugs *between* browsers so the same element renders the same way everywhere.
- Reset = "give me nothing, I'll style everything myself." Normalize = "give me sensible, consistent defaults I can build on top of."
- The reset approach means more up-front work (you must explicitly style basic things like list markers) but total control; normalize means less up-front work but you inherit some default styling you may need to override anyway.
- Most modern projects use neither extreme — they reach for a short, targeted **"modern reset"** (a handful of lines: `box-sizing: border-box`, removing default margins, capping `img` width) that borrows the best of both without the baggage of either.

---

## ⚙️ How It Works

Imagine you're handed a house to renovate. A classic **reset** is like gutting it down to the studs — no flooring, no fixtures, no paint, nothing left of the original builder's choices. You get total creative freedom, but now you also have to install literal flooring before you can put a chair anywhere. That's what Eric Meyer's reset does to `<h1>`–`<h6>`, `<ul>`, `<ol>`, `<blockquote>`, and dozens of other elements: it zeroes out their margin, padding, font-size, and font-weight so they render as visually identical, unstyled blocks of text until *you* say otherwise.

**normalize.css**, by contrast, is like a contractor who walks through the house and fixes the things that are actually broken or inconsistent — squeaky doors, mismatched trim, a light switch wired backwards — but leaves the kitchen cabinets and working plumbing alone because they're already fine. It preserves the fact that an `<h1>` looks bigger and bolder than a `<p>`, that a `<ul>` has bullets, that a `<sup>` is superscript — but patches over browser-specific bugs (like Safari's odd `<button>` font inheritance, or inconsistent `<hr>` sizing) so those elements behave the same everywhere.

The modern trend acknowledges that most projects don't need either extreme. You're usually going to define your own type scale and spacing system anyway (so you don't need normalize's "preserve sensible heading sizes" behavior), but you also don't need to zero out *literally everything* the way a full reset does. A **modern reset** picks the small number of browser defaults that reliably cause pain — inconsistent box-sizing, default body/heading margins that don't match your design system's spacing scale, images that overflow their container, list markers you don't want on nav menus — and neutralizes just those, in a handful of lines, leaving everything else alone.

---

## 💻 Examples

```css
/* Excerpt from the spirit of Eric Meyer's classic Reset CSS —
   zeroes out nearly everything, forcing intentional styling later */
html, body, div, span, applet, object, iframe,
h1, h2, h3, h4, h5, h6, p, blockquote, pre,
a, abbr, acronym, address, big, cite, code,
del, dfn, em, img, ins, kbd, q, s, samp,
small, strike, strong, sub, sup, tt, var,
b, u, i, center,
dl, dt, dd, ol, ul, li,
fieldset, form, label, legend,
table, caption, tbody, tfoot, thead, tr, th, td {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
ol, ul { list-style: none; } /* bullets gone — you re-add them where wanted */
```

```css
/* Excerpt from the spirit of normalize.css — fixes bugs, keeps sensible defaults */
/* Correct the font size and margin on h1 elements within section/article
   contexts, a known inconsistency across browsers */
h1 {
  font-size: 2em;
  margin: 0.67em 0;
}

/* Remove the gray background on active links in IE 10 */
a {
  background-color: transparent;
}

/* Add the correct box sizing in Firefox for legend elements */
legend {
  box-sizing: border-box;
  color: inherit;
  display: table;
  max-width: 100%;
}
```

```css
/* A modern reset — the popular middle-ground approach (loosely after
   Josh Comeau / Andy Bell's public "modern CSS reset" writeups) */
*, *::before, *::after {
  box-sizing: border-box; /* padding/border no longer blow out widths */
}

* {
  margin: 0; /* remove all default margins — you set spacing intentionally */
}

body {
  line-height: 1.5; /* sensible readable default line-height everywhere */
  -webkit-font-smoothing: antialiased;
}

img, picture, video, canvas, svg {
  display: block;
  max-width: 100%; /* images never overflow their container */
}

input, button, textarea, select {
  font: inherit; /* form controls inherit page typography instead of
                     defaulting to the OS's ugly system font */
}
```

---

## 🚀 Real World Applications

- Starting any new project by choosing one of these three approaches as the very first stylesheet loaded, before any component styles.
- Debugging "this looks fine in Chrome but the spacing is off in Safari" — often traceable to a missing reset/normalize step, or one that only partially covers the affected element.
- Working inside a design system (Tailwind, Bootstrap, Material) that ships its own baked-in reset (Tailwind's Preflight is a modern reset; Bootstrap ships a normalize-derived Reboot) — recognizing that layer exists explains a lot of "default" behavior you didn't write.
- Auditing legacy code that still carries Eric Meyer's full reset, understanding *why* every `<ul>` in that codebase needs explicit `list-style` re-declared.

---

## ⚖️ Advantages

- **Full reset:** total predictability — nothing renders with styling you didn't explicitly write, which is valuable in highly custom design systems where "default" browser looks would clash anyway.
- **normalize.css:** less boilerplate for content-heavy sites (blogs, docs) where semantic elements should still look roughly like themselves out of the box.
- **Modern reset:** small, fast, easy to read in full, and targets only the handful of defaults that reliably cause real problems — the best return on effort for most projects today.

---

## ⚠️ Limitations

- **Full reset:** you now owe yourself *every* basic style — list bullets, heading hierarchy, table borders — which is a lot of upfront work and easy to forget pieces of (a common source of "why doesn't this list have bullets" bugs on inherited codebases).
- **normalize.css:** as an external dependency it's another file to load, and its bug-fix list gets less relevant every year as browsers converge — some of its rules target IE and old Android quirks few sites still support.
- **Modern reset:** being minimal by design, it deliberately leaves some cross-browser inconsistencies (button appearance, form control theming, focus ring styling) unaddressed — you're expected to handle those deliberately, not by default.

---

## 🚨 Common Mistakes

- Loading *both* a full reset and normalize.css together — they're alternative philosophies, not complementary layers, and combining them just adds unnecessary CSS weight and confusing overrides.
- Using a full reset and then forgetting to restore `list-style` on navigation `<ul>`s, leaving default bullets on menus that were clearly meant to be reset intentionally, or the opposite — forgetting basic content lists lost their bullets.
- Assuming a framework (Bootstrap, Tailwind) has "no reset" just because you didn't write one — most ship their own, and not knowing that leads to confusing debugging when defaults don't match raw-browser expectations.
- Writing a reset/normalize block *after* other styles in the cascade instead of first, causing it to unintentionally override intentional component styles due to source order.
- Not accounting for `box-sizing: border-box` as part of whichever approach is chosen — it's arguably the single highest-value line in any of these strategies and is easy to omit if hand-rolling a "reset."

---

## 📖 Further Reading

- Eric Meyer: "CSS Tools: Reset CSS"
- necolas/normalize.css (GitHub) — the canonical normalize.css source and changelog comments explaining each fix
- Josh Comeau: "The New CSS Reset" (a widely cited modern-reset writeup)
- Andy Bell: "A (more) Modern CSS Reset"

---

## 💡 Wisdom from Mímir

I used to reach for Eric Meyer's reset out of habit on every project, and it always cost me the same afternoon re-adding list bullets to a nav bar I'd forgotten I'd zeroed out. These days I keep a five-line modern reset saved in a snippet manager — `box-sizing`, margin removal, `img` max-width, form-control font inheritance — and that's it. It's not about picking the "correct" philosophy; it's about recognizing that both classic reset and normalize.css were solutions to a browser-inconsistency landscape that's mostly gone. Modern evergreen browsers already agree on most of the details those two were built to fix.

---

## 🔗 Related Notes

- [[The Box Model]] — `box-sizing: border-box`, the cornerstone of any modern reset, is a box-model concept
- [[The Cascade and Inheritance]] — resets work by exploiting the cascade, sitting first so your own styles override them predictably
- [[Understanding Specificity]] — a good reset uses low-specificity selectors (type selectors, `*`) so it's trivially easy to override later
- [[CSS Variables (Custom Properties)]] — often paired with a modern reset to establish a project's baseline spacing/type scale
