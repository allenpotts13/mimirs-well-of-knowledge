---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: HTML
difficulty: Beginner
tags:
  - forms
  - navigation
  - accessibility
  - semantics
aliases:
  - "Buttons vs Links: button vs a"
  - button vs a
  - When to Use a Link vs a Button
publish: true
permalink: html/buttons-vs-links-button-vs-a
---

# <span class="rune">ᛟ</span> Buttons vs Links: button vs a

> *`<a href>` takes the user somewhere; `<button>` does something — mixing the two up quietly breaks keyboard navigation, screen readers, and "open in new tab" for everyone who relies on them.*

---

## 🎯 Purpose

Visually, a styled `<a>` and a styled `<button>` can be pixel-identical — same rounded corners, same hover state, same padding. But the browser treats them completely differently under the hood: one is a navigation primitive, the other is an interaction primitive. This concept exists because picking the wrong one doesn't just offend a purist — it silently removes real, expected browser behavior (middle-click to open in a new tab, right-click "copy link," Enter *and* Space to activate, correct screen-reader announcement) that users don't know they're missing until it's gone.

---

## 🧠 Key Ideas

- **`<a href="...">` means "go somewhere"** — a new page, a different section of the same page (`#section-id`), a file download, a `mailto:` or `tel:` link.
- **`<button>` means "do something right here"** — submit a form, open a modal, toggle a menu, increment a counter — anything that does *not* change the URL.
- A real `<a href>` is natively middle-clickable, ctrl/cmd-clickable to open in a new tab, draggable to a bookmark bar, and shows a destination preview in the status bar — none of which JavaScript can fully replicate on a fake link.
- A real `<button>` is natively focusable, activates on both **Enter and Space**, and is announced by screen readers as "button" with its accessible name — an `<a>` only activates on Enter and is announced as "link."
- `href="#"` or a missing `href` turns an `<a>` into a broken promise: it *looks* like a link (cursor, styling, screen-reader "link" role) but goes nowhere and often needs a stray `event.preventDefault()` to stop the page from jumping to the top.

---

## ⚙️ How It Works

Ask one question: **after this is activated, does the URL change (or does the browser navigate)?** If yes — even just to a `#fragment` on the same page — it's an `<a href>`. If no — the page stays exactly where it is and something on the page changes (a form submits, a panel opens, a value updates) — it's a `<button>`.

The trap is `<a onclick="doThing()">` with no real `href`, or `href="#"`/`href="javascript:void(0)"`. It's built to *look* like a link but wired to *behave* like a button, and it inherits the worst of both: it doesn't reliably work with Space to activate (links only fire on Enter), it can't be opened in a new tab or middle-clicked usefully, and `href="#"` actively jumps the scroll position to the top of the page unless you remember to suppress the default action in JavaScript. Screen readers also announce it as a "link," setting an expectation — "this takes me somewhere" — that the actual behavior violates.

```html
<!-- Looks like a button, but is secretly a broken link -->
<a href="#" onclick="submitForm(); return false;">Submit</a>
<!-- No real destination, needs manual scroll-jump prevention,
     misannounced to screen readers as a "link" -->

<!-- What it should be -->
<button type="submit">Submit</button>
```

---

## 💻 Examples

```html
<!-- Navigation: a real destination, so it's a link -->
<a href="/pricing">See pricing</a>
<a href="#faq">Jump to FAQ</a>
<a href="/report.pdf" download>Download the report</a>

<!-- Action: no destination, so it's a button -->
<button type="button" id="toggle-menu">Menu</button>
<button type="submit">Create account</button>
<button type="button" onclick="modal.showModal()">Open settings</button>
```

```html
<!-- A "link-styled" button — visually a link, semantically correct -->
<button type="button" class="link-style">Dismiss</button>

<style>
  .link-style {
    background: none;
    border: none;
    color: blue;
    text-decoration: underline;
    cursor: pointer;
    padding: 0;
    font: inherit;
  }
</style>
<!-- Looks like a text link, but it's a real <button> because
     clicking it doesn't navigate anywhere -->
```

```html
<!-- A "button-styled" link — visually a button, semantically correct -->
<a href="/signup" class="btn-style">Sign up</a>

<style>
  .btn-style {
    display: inline-block;
    padding: 0.6em 1.2em;
    background: #2563eb;
    color: white;
    border-radius: 6px;
    text-decoration: none;
  }
</style>
<!-- Looks like a button, but it IS a real link — middle-click,
     "open in new tab," and status-bar preview all still work -->
```

---

## 🚀 Real World Applications

- "Add to cart" is a `<button>` (state changes, URL doesn't); "View cart" is an `<a href="/cart">` (navigates to a new page).
- A single-page app's "Learn more" that expands an inline description in place is a `<button>`; the same text linking out to a full article page is an `<a>`.
- Pagination controls ("Next page") are almost always links, since each page typically has its own URL (`?page=2`) that should be bookmarkable and shareable.
- A hamburger menu icon that opens/closes an off-canvas nav is a `<button>` — nothing navigates, a panel just toggles visibility.
- "Log out" is debated but is usually best as a `<button>` inside a form doing a POST, since logging out changes server-side session state rather than simply navigating to a page.

---

## ⚖️ Advantages

- Correct choice gives you keyboard accessibility, screen-reader announcements, and browser-native behaviors for free, with zero JavaScript.
- Users build muscle memory around link vs. button behavior (middle-click opens links in a new tab, Space activates buttons) — matching that expectation reduces confusion and support complaints.
- Search engines follow `<a href>` links to crawl and index pages; a `<button onclick="navigate()">` is invisible to a crawler, so faking navigation with a button actively hurts discoverability.
- CSS can make either element look like literally anything, so there's no visual reason to ever pick the semantically wrong one.

---

## ⚠️ Limitations

- Some genuinely ambiguous cases exist (e.g., a "Read more" that both expands text *and* eventually links to a full page) — pick based on the *primary* action.
- Styling a `<button>` to perfectly reset all browser default appearance (border, background, font inheritance) takes a few extra CSS lines that a bare `<a>` doesn't need.
- Forms and buttons interact in easy-to-miss ways — a `<button>` inside a `<form>` defaults to `type="submit"` unless you explicitly set `type="button"`, which trips people up when they just wanted a non-submitting action button.

---

## 🚨 Common Mistakes

- Using `<a href="#">` or `<a href="javascript:void(0)">` as a general-purpose clickable element instead of `<button>`.
- Forgetting `type="button"` on a `<button>` inside a `<form>` that isn't meant to submit, causing an accidental form submission.
- Styling a `<div>` or `<span>` with `onclick` instead of using either element — losing keyboard focusability and semantics entirely (see [[div and span - Generic Containers and When to Avoid Them]]).
- Using a `<button>` with JavaScript to navigate (`onclick="location.href='/page'"`) instead of a plain `<a href="/page">`, which breaks middle-click, right-click "open in new tab," and crawler indexing.
- Disabling a link visually with CSS instead of removing/replacing its `href`, leaving it still focusable and clickable via keyboard despite looking "disabled."

---

## 📖 Further Reading

- MDN: `<a>` element reference
- MDN: `<button>` element reference
- The A11Y Project: "Links vs. Buttons in Modern Web Applications"

---

## 💡 Wisdom from Mímir

Every time I've been tempted to reach for `href="#"`, it's because I was thinking about what the element *looks like* instead of what it *does*. The fix is always the same: stop styling first and ask "does this change the URL?" The answer picks the tag, and only then do you touch CSS — because CSS can make either element look like anything, but it can never give a `<div>` the keyboard behavior a real `<a>` or `<button>` gets for free.

---

## 🔗 Related Notes

- [[Semantic HTML and Why It Matters]] — the general principle this is a specific, extremely common instance of
- [[div and span - Generic Containers and When to Avoid Them]] — the even more broken version of this mistake
- [[Accessibility Basics - alt Text, Labels, and ARIA]] — why native focusability and roles matter to real users
- [[Forms and Form Validation]] — where `<button type="submit">` actually lives and does its job
