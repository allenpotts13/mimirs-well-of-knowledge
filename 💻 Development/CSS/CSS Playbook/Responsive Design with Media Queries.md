---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: CSS
difficulty: Beginner
tags:
  - responsive-design
  - media-queries
  - mobile-first
  - fundamentals
aliases:
  - Media Queries
  - Mobile-First CSS
  - Responsive CSS
publish: true
---

# 📚 Responsive Design with Media Queries

> *Media queries let you apply CSS conditionally based on the viewport (or device) — the mechanism that makes one codebase adapt from a phone screen to a widescreen monitor instead of needing separate sites for each.*

---

## 🎯 Purpose

A layout that looks great on a 27-inch monitor is often unusable on a phone, and vice versa — three-column layouts don't fit in 375px, and giant touch targets waste space on desktop. Before media queries, "responsive" meant separate mobile sites (`m.example.com`) with duplicated markup and logic. Media queries exist so a single stylesheet — and often a single set of HTML — can adapt its layout, sizing, and even content visibility based on the size (and other characteristics) of the screen it's rendered on.

---

## 🧠 Key Ideas

- A media query wraps CSS in a condition: `@media (condition) { ...rules... }` — the enclosed rules only apply when the condition is true.
- **Mobile-first** uses `min-width` queries — you write base styles for the smallest screen first, then layer on *additional* styles as the viewport grows past each breakpoint.
- **Desktop-first** uses `max-width` queries — you write base styles for the largest screen first, then *override* them as the viewport shrinks past each breakpoint.
- Common breakpoint conventions cluster around real device/content boundaries rather than exact device widths: roughly `480px`/`576px` (large phones), `768px` (tablets), `1024px`/`992px` (small laptops), `1280px`/`1440px` (desktops) — but the best breakpoints are wherever *your specific content* starts to look cramped or awkward, not a memorized list.
- Multiple conditions combine with `and`: `@media (min-width: 768px) and (max-width: 1023px) { ... }` targets a specific range (e.g. "tablet only").
- Mobile-first is generally the better default: it forces you to prioritize core content and functionality for the most constrained case first, and it results in a simpler cascade (adding styles as space becomes available) rather than an overriding one (undoing desktop assumptions as space shrinks).

---

## ⚙️ How It Works

**Mobile-first** reads like building up: start with a single-column, essentials-only layout that works on the smallest realistic screen, then say "once we have at least 768px of width, *also* do this" — laying two columns side by side, say. Each `min-width` query only ever *adds* capability as the viewport grows. Nothing needs to be undone.

**Desktop-first** reads like tearing down: start with the full, spacious multi-column desktop layout, then say "once we're down to at most 767px, *override* this" — collapsing columns back to one, shrinking font sizes, hiding a sidebar. Each `max-width` query has to actively *undo* desktop assumptions that don't fit anymore.

The practical difference shows up in cascade complexity. With mobile-first, your base (unqueried) styles are the simplest possible version of the UI, and every query only adds — there's rarely a fight between competing rules. With desktop-first, your base styles are the *most complex* version, and every query is playing defense, unwinding specific desktop decisions one at a time; it's easy to forget to override something and get a broken hybrid layout on a mid-size screen.

There's also a content-strategy argument for mobile-first, not just a technical one: designing for the smallest screen first forces hard, useful decisions about what's actually essential ("does this really need to be visible on every screen size, or was it just filling empty desktop space?") — decisions that are easy to skip if you start from the roomiest layout and only think about paring down later.

---

## 💻 Examples

```css
/* Mobile-first — base styles are for the smallest screen, then layer up */
.layout {
  display: flex;
  flex-direction: column;   /* single column by default (mobile) */
}

@media (min-width: 768px) {
  .layout {
    flex-direction: row;    /* side-by-side once there's room (tablet+) */
  }
}

@media (min-width: 1280px) {
  .layout {
    max-width: 1200px;      /* cap width once there's a LOT of room */
    margin-inline: auto;
  }
}
```

```css
/* Desktop-first — base styles are for the largest screen, then tear down */
.layout {
  display: flex;
  flex-direction: row;      /* side-by-side by default (desktop) */
  max-width: 1200px;
  margin-inline: auto;
}

@media (max-width: 1279px) {
  .layout {
    max-width: none;        /* undo the desktop width cap */
  }
}

@media (max-width: 767px) {
  .layout {
    flex-direction: column; /* undo the side-by-side layout */
  }
}
```

```css
/* Combining conditions with "and" — target a specific range */
@media (min-width: 768px) and (max-width: 1023px) {
  .sidebar {
    display: none; /* hide the sidebar only in this tablet-width range */
  }
}
```

```css
/* Media queries aren't just about width */
@media (prefers-color-scheme: dark) {
  body { background: #111; color: #eee; }
}

@media (orientation: landscape) {
  .hero { min-height: 100vh; }
}
```

---

## 🚀 Real World Applications

- Navigation that's a hamburger menu on mobile and a full horizontal nav bar once there's enough width.
- Card grids that go from 1 column on phones, to 2 on tablets, to 4 on desktop using stacked `min-width` breakpoints.
- Hiding decorative or secondary content (large hero images, sidebars) below a certain width to prioritize core content on small screens.
- Adjusting touch target sizes and spacing for touch-primary (mobile) vs. mouse-primary (desktop) contexts.
- Respecting user preferences like `prefers-color-scheme` (dark mode) or `prefers-reduced-motion` via the same media query mechanism.

---

## ⚖️ Advantages

- Single codebase adapts to all screen sizes — no duplicated mobile-site maintenance burden.
- Mobile-first specifically produces simpler, additive CSS that's easier to reason about and debug.
- Works entirely in CSS, no JavaScript required, and degrades gracefully in unsupported/older contexts.
- Extends beyond just viewport width — media features like `prefers-color-scheme`, `prefers-reduced-motion`, and `orientation` let CSS respond to more than just screen size.

---

## ⚠️ Limitations

- Media queries respond to the **viewport**, not the actual **container** an element sits in — a sidebar widget can't independently ask "how much space do *I* have," which is what [[Container Queries]] were introduced to solve.
- Breakpoint proliferation (too many arbitrary breakpoints) can make a stylesheet hard to maintain — it's easy to end up with breakpoints that don't correspond to any real content need.
- Desktop-first codebases inherited from older projects can be genuinely painful to convert to mobile-first without a significant rewrite.
- Testing every breakpoint combination thoroughly (especially combined with dark mode, reduced motion, print styles, etc.) is easy to under-invest in.

---

## 🚨 Common Mistakes

- Picking breakpoints from a generic "device size" chart instead of testing where *your own content* actually breaks and choosing breakpoints there.
- Writing desktop-first by habit (it often feels more "natural" since design mockups are usually desktop-sized) and ending up with more override CSS than necessary.
- Forgetting `<meta name="viewport" content="width=device-width, initial-scale=1">` in the HTML `<head>` — without it, mobile browsers render at a zoomed-out desktop-width viewport and media queries behave unexpectedly.
- Using `max-width` and `min-width` queries inconsistently within the same project, creating a confusing mixed mental model of which direction styles "layer."
- Forgetting that media queries only see the **viewport**, not the actual rendered width of the specific component — leading to reflexive `@media` use where a [[Container Queries]] approach would actually be more correct.

---

## 📖 Further Reading

- MDN: "Using media queries"
- MDN: "Responsive design"
- A List Apart: "Responsive Web Design" (Ethan Marcotte's original 2010 article that coined the term)

---

## 💡 Wisdom from Mímir

Mobile-first isn't just a technical preference of mine, it's a forcing function: when you're not allowed to lean on a spacious desktop canvas as your starting point, you're forced to ask "does this content actually matter enough to earn its place on the smallest screen?" for every single piece of the page. That question alone has killed more unnecessary UI clutter in my projects than any design review ever has. Start cramped, on purpose — it's a feature, not a limitation.

---

## 🔗 Related Notes

- [[Mobile-First vs Desktop-First Design]] — a deeper dive into the design-process side of this same decision, beyond just the CSS mechanics
- [[Container Queries]] — the newer, complementary tool for when a component needs to respond to its container's size, not the viewport's
- [[CSS Units - px, em, rem, %, and vw-vh]] — responsive layouts typically pair media queries with relative units so scaling stays proportional
- [[CSS Grid Layout Fundamentals]] — `auto-fill`/`minmax()` grids can reduce how many media queries a layout needs in the first place
