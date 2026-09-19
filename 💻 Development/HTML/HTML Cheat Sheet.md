---
publish: true
status: published
permalink: html/html-cheat-sheet
---

# <span class="rune">ᛊ</span> HTML Cheat Sheet

> Dense reference across core HTML. Each section links back to the full [[HTML Codex|concept note]] for depth.

---

## Document Structure

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Page Title</title>
</head>
<body>
    <!-- content -->
</body>
</html>
```

```html
<h1>Only ONE per page</h1>       <!-- top of the document outline -->
<h2>Section heading</h2>
<h3>Subsection heading</h3>       <!-- never skip levels for styling reasons — screen readers rely on this -->

<!-- this is a comment, never rendered -->

&lt; &gt; &amp; &quot; &copy; &nbsp;   <!-- entities — needed for reserved/special characters -->
```

See: [[Document Structure - doctype, html, head, and body]] · [[Headings and Document Outline]] · [[Comments in HTML]] · [[Character Encoding and HTML Entities]]

---

## Semantic HTML

```html
<header>...</header>     <!-- intro / branding content -->
<nav>...</nav>            <!-- primary navigation links -->
<main>...</main>           <!-- the ONE main content area, exactly once per page -->
<article>...</article>      <!-- self-contained, independently distributable content -->
<section>...</section>       <!-- a thematic grouping, usually with its own heading -->
<aside>...</aside>            <!-- tangentially related content (sidebar, pull-quote) -->
<footer>...</footer>           <!-- closing/meta content -->

<div>...</div>    <!-- generic container — NO semantic meaning, layout/styling hook only -->
<span>...</span>   <!-- generic INLINE container — same, but inline -->
```

**Reach for a semantic element first. `div`/`span` are the fallback when nothing semantic fits.**

```html
<ul><li>Unordered item</li></ul>
<ol><li>Ordered item</li></ol>
<dl><dt>Term</dt><dd>Description</dd></dl>

<table>
  <thead><tr><th>Header</th></tr></thead>
  <tbody><tr><td>Data</td></tr></tbody>
</table>
```

See: [[Semantic HTML and Why It Matters]] · [[Semantic Sectioning Elements - header, nav, main, article, section, aside, footer]] · [[div and span - Generic Containers and When to Avoid Them]] · [[Lists - Ordered, Unordered, and Description]] · [[Tables for Tabular Data]]

---

## Forms

```html
<form action="/submit" method="POST">
    <label for="email">Email</label>
    <input type="email" id="email" name="email" required>

    <input type="text" placeholder="Name">
    <input type="password">
    <input type="checkbox" checked>
    <input type="radio" name="choice">
    <input type="date">
    <input type="number" min="0" max="10">

    <button type="submit">Submit</button>     <!-- submits the form -->
    <button type="button">Click Me</button>    <!-- does NOT submit — needs JS -->
</form>
```

| Method | Visible in URL? | Idempotent? | Use for |
|---|---|---|---|
| `GET` | Yes — data in query string | Yes | Retrieving data, searches, bookmarkable requests |
| `POST` | No — data in request body | No | Submitting data, creating/mutating server state |

**`<label for="id">` linked to a matching `<input id="id">` is what makes a form field accessible and click-to-focus.**

See: [[Forms and Form Validation]] · [[Input Types and Attributes]] · [[The label Element and Form Accessibility]] · [[Form Submission - GET vs POST]] · [[Buttons vs Links - button vs a]]

---

## Media & Embedding

```html
<img src="photo.jpg" alt="Description of the image" width="600" height="400">

<img srcset="small.jpg 480w, large.jpg 1080w"
     sizes="(max-width: 600px) 480px, 1080px"
     src="large.jpg" alt="Responsive image">

<picture>
    <source media="(max-width: 600px)" srcset="mobile.jpg">
    <img src="desktop.jpg" alt="Fallback and default">
</picture>

<video controls src="video.mp4"></video>
<audio controls src="audio.mp3"></audio>
<iframe src="https://example.com" title="Embedded content"></iframe>
```

`alt` is required for every meaningful `<img>` — empty `alt=""` is correct ONLY for purely decorative images.

See: [[Images and Responsive Images (srcset and picture)]] · [[Embedding Media - audio, video, and iframe]] · [[Web Components Basics - Custom Elements and Templates]]

---

## Accessibility & SEO

```html
<button aria-label="Close dialog">✕</button>       <!-- accessible name when visible text isn't enough -->
<div role="alert">Important message</div>            <!-- ARIA role — use native elements FIRST, ARIA as a last resort -->
<img alt="">                                           <!-- decorative-only image, correctly empty -->

<meta name="description" content="A concise page summary for search results.">
<meta property="og:title" content="Page Title">        <!-- Open Graph — social share previews -->
```

**The first rule of ARIA: don't use ARIA if a native semantic element already does the job.**

See: [[Accessibility Basics - alt Text, Labels, and ARIA]] · [[Meta Tags and Basic SEO]]

---

## Attributes & Navigation

```html
<div data-user-id="42" data-role="admin">...</div>   <!-- custom data attributes -->
<script>
    el.dataset.userId;   // "42" — camelCase access in JS
</script>

<a href="/page">Relative link</a>
<a href="https://example.com" target="_blank" rel="noopener noreferrer">External link</a>
<a href="#section-id">Jump to a section on this page</a>
```

`rel="noopener noreferrer"` on `target="_blank"` links prevents the new page from accessing `window.opener` — a real security consideration, not just convention.

See: [[Data Attributes (data-＊)]] · [[Links and Navigation Best Practices]]

---

## Quick Reference

See: [[Common HTML elements]]

---

## 🔗 Related Notes

- [[HTML Codex]]
- [[CSS Cheat Sheet]]
