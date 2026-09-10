---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-06
technology: HTML
difficulty: Beginner
tags:
  - semantics
  - page-structure
  - landmarks
  - accessibility
aliases:
  - "Semantic Sectioning Elements: header, nav, main, article, section, aside, footer"
  - header nav main article section aside footer
  - HTML5 Sectioning Elements
  - Landmark Elements
publish: true
permalink: html/semantic-sectioning-elements-header-nav-main-article-section-aside-footer
---

# <span class="rune">ᛟ</span> Semantic Sectioning Elements: header, nav, main, article, section, aside, footer

> *These seven elements give a page's major regions real meaning — "this is the navigation," "this is the one unique main content," "this stands alone" — instead of everything being an anonymous `<div>`.*

---

## 🎯 Purpose

Before HTML5, every page region was a `<div class="header">`, `<div class="nav">`, `<div class="footer">` — visually structured, but meaningless to anything except a human reading the class names. These elements exist to give the *browser itself* (and screen readers, and search engines) a standard vocabulary for a page's major regions, so that structure is understood automatically instead of only by convention.

---

## 🧠 Key Ideas

- `<header>` and `<footer>` are **not one-per-page** — they can appear once per `<article>` or `<section>` too (a blog post's own byline/date block is a legitimate `<header>` inside that `<article>`).
- `<nav>` is for a set of **major** navigation link groups (primary site nav, a table of contents) — not every random cluster of links on a page needs to be wrapped in one.
- `<main>` should appear **exactly once** per page, wrapping the primary content that is unique to that page (not the header, nav, or footer that repeat across every page of the site).
- `<article>` is for content that would make sense **standalone** — syndicated in a feed, shared on its own, pulled out of context entirely (a blog post, a forum comment, a product card).
- `<section>` is a **thematic grouping** of content, and should generally have its own heading — it is not just a generic styling wrapper (that's what `<div>` is for).
- `<aside>` is for content that's **tangentially related** to the surrounding content — a sidebar, a pull-quote, a "related links" box — not primary content itself.

---

## ⚙️ How It Works

Think of these elements as labeled rooms in a house, each announced by a sign on the door instead of left for a visitor to guess at from the furniture inside. A screen reader user (or a browser's own accessibility tree) can jump directly to "navigation," "main content," or "the footer" the same way a sighted visitor's eye is drawn to a page's visual layout — but only if the *right room* is used for the *right purpose*. Using `<div>` for everything is like removing every sign from every door: still a functioning house, but only navigable by trial and error.

---

## 💻 Examples

```html
<body>
  <header>
    <h1>My Site</h1>
    <nav>
      <a href="/">Home</a>
      <a href="/blog">Blog</a>
      <a href="/contact">Contact</a>
    </nav>
  </header>

  <main>
    <article>
      <header>
        <h2>How Closures Work</h2>
        <p>Posted <time datetime="2026-09-02">September 2, 2026</time></p>
      </header>

      <p>A closure is a function that remembers...</p>

      <section>
        <h3>Common Mistakes</h3>
        <p>...</p>
      </section>

      <footer>
        <p>Tags: javascript, closures</p>
      </footer>
    </article>

    <aside>
      <h3>Related Posts</h3>
      <ul>
        <li><a href="/hoisting">Understanding Hoisting</a></li>
      </ul>
    </aside>
  </main>

  <footer>
    <p>&copy; 2026 My Site. All rights reserved.</p>
  </footer>
</body>
```

Notice `<header>` and `<footer>` appear **twice** — once for the whole page, once for the individual `<article>` — and that's entirely correct usage.

---

## 🚀 Real World Applications

- A screen reader's "landmarks" navigation menu lets a user jump straight to `<nav>`, `<main>`, or a named `<aside>` without tabbing through the entire page first.
- RSS/Atom feed generators and "reader mode" browser features look for `<article>` boundaries to extract clean, standalone content.
- Search engines use `<main>` and `<article>` to identify a page's actual unique content versus its repeated site-wide chrome (header/nav/footer), which affects how a page is indexed and summarized.
- A component library can safely reuse the same `<article>` markup for a blog post, a product card, or a forum reply, because the element's meaning ("this stands alone") is exactly what all three have in common.

---

## ⚖️ Advantages

- Gives browsers, screen readers, and search engines a shared, standard vocabulary for page regions with zero extra code.
- Self-documenting — a developer reading `<nav>` or `<aside>` immediately understands its role, unlike `<div class="sidebar-2">`.
- Landmark navigation is a huge, free accessibility win for screen reader and keyboard users.

---

## ⚠️ Limitations

- These elements convey *structure*, not visual layout — none of them come with meaningful default styling, so people sometimes wrongly assume using them "does" something visually.
- The line between `<section>` and `<div>` is genuinely fuzzy in edge cases — the practical test ("does this need its own heading and represent a distinct theme?") isn't always obvious.
- Overlapping conventions exist across frameworks and design systems (e.g. some component libraries use "section" to mean something else entirely), which can create naming confusion on a team.

---

## 🚨 Common Mistakes

- Wrapping every `<div>` in a `<section>` out of habit, without a distinct theme or heading — this is just "divitis" with extra steps and doesn't add real semantic value.
- Using more than one `<main>` per page, or nesting `<main>` inside `<article>`/`<section>`/`<aside>` (it's meant to be a page-level landmark, not a nested one).
- Wrapping *every* group of links in `<nav>`, including things like a simple inline list of tags or a single "read more" link — `<nav>` should be reserved for major navigation blocks.
- Using `<aside>` for content that's actually central to the page, just because it's visually placed in a sidebar.
- Forgetting that `<header>`/`<footer>` can and should be scoped inside `<article>`/`<section>` when that content has its own byline, date, or metadata block.

---

## 📖 Further Reading

- MDN: "Document and website structure"
- The W3C/WHATWG HTML specification's "Sections" chapter
- web.dev: "Learn HTML: Document structure"

---

## 💡 Wisdom from Mímir

My test for `<section>` versus `<div>` is simple: **if I gave this block its own heading, would that heading make sense?** If yes, it's a `<section>`. If the block is purely a visual/layout convenience with no thematic identity of its own, it's a `<div>`, and that's the correct, non-lazy choice — not every wrapper needs to be "more semantic than a div." Semantics should describe something true about the content, not just make the markup look fancier.

---

## 🔗 Related Notes

- [[Semantic HTML and Why It Matters]] — the general principle these seven elements are a specific vocabulary for
- [[div and span - Generic Containers and When to Avoid Them]] — the deliberate, correct choice when no sectioning element actually fits
- [[Headings and Document Outline]] — `<section>`'s heading requirement ties directly into a page's overall heading structure
- [[Accessibility Basics - alt Text, Labels, and ARIA]] — landmark navigation is one of the biggest accessibility wins these elements provide for free
