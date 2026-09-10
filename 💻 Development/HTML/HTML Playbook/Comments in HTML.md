---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: HTML
difficulty: Beginner
tags:
  - fundamentals
  - security
  - syntax
aliases:
  - HTML Comment Syntax
  - "<!-- -->"
publish: true
permalink: html/comments-in-html
---

# <span class="rune">ᛟ</span> Comments in HTML

> *`<!-- -->` hides text from the rendered page, but not from anyone who views the source — comments are for notes to fellow developers, never for secrets.*

---

## 🎯 Purpose

Markup can get complex enough that a future reader (often you, six months later) benefits from a short explanation of *why* something is structured a certain way, or a marker showing where a generated block begins and ends. HTML comments exist to carry that kind of note through the file without the browser rendering it as visible content — but because they're stripped only from the visual page, not from the actual bytes sent to the browser, they exist in a strange middle ground: invisible to a typical visitor, fully visible to anyone who opens dev tools or "View Source."

---

## 🧠 Key Ideas

- Syntax is `<!-- comment text here -->` — everything between `<!--` and `-->` is ignored by the renderer, and can span multiple lines.
- Comments are **not private** — they're delivered to the browser as plain text in the HTML response and are trivially visible via "View Page Source" or browser dev tools. Never put API keys, passwords, internal notes, or "TODO: fix this security hole" in one.
- A very common legitimate use is **marking the boundaries of a generated or templated section** (e.g. `<!-- Start: Header Component -->` … `<!-- End: Header Component -->`), which helps when scanning a large file built by a template engine or CMS.
- Another common use is **temporarily disabling a block of markup** during development, without deleting it — comment it out, keep working, uncomment later.
- Comments cannot be nested — `<!-- outer <!-- inner --> still outer -->` doesn't work as you'd expect, because the parser closes the comment at the *first* `-->` it finds.

---

## ⚙️ How It Works

The browser's HTML parser recognizes the `<!--` sequence as "start ignoring content" and `-->` as "resume normal parsing," and everything in between — tags, text, whatever — is skipped entirely for rendering purposes. This makes comments useful for silencing a whole chunk of markup at once while testing, since you don't need to worry about the specific tags inside being valid or complete on their own; the parser isn't even looking at them as markup while commented out.

But "ignored by the renderer" is not the same as "removed from the response." The comment text is still sitting in the raw HTML the server sent — which means it travels over the network and lands in the browser's memory just like everything else. Opening dev tools' "Elements" or "Sources" panel, or choosing "View Page Source," shows it in full. Treat an HTML comment the same way you'd treat a sticky note left on the *outside* of a filing cabinet, not a locked drawer — anyone walking by can read it.

```html
<!-- This is a single-line comment -->

<!--
  This is a multi-line comment.
  Useful for longer explanations of why a section
  is structured the way it is.
-->
```

---

## 💻 Examples

```html
<!-- Marking the start/end of a CMS-generated section, useful when
     scanning a large templated file to find hand-written vs. generated markup -->
<!-- Start: Featured Products Widget -->
<section class="featured-products">
  <h2>Featured Products</h2>
  <!-- ... generated product cards ... -->
</section>
<!-- End: Featured Products Widget -->
```

```html
<!-- Temporarily disabling a block during development, without deleting it -->
<nav>
  <a href="/">Home</a>
  <a href="/about">About</a>
  <!--
  <a href="/beta-feature">Beta Feature</a>
  -->
  <a href="/contact">Contact</a>
</nav>
<!-- The beta-feature link is hidden from users but still in the file,
     ready to be re-enabled by removing the comment markers -->
```

```html
<!-- What NOT to do: comments are visible to anyone via "View Source" -->

<!-- TODO: hardcoded API key below, remove before launch: sk_live_abc123 -->
<script>
  const apiKey = "sk_live_abc123";
</script>
<!-- Never do this — the comment doesn't hide the key, it just
     draws extra attention to it for anyone reading the source -->
```

---

## 🚀 Real World Applications

- Template engines and static site generators often auto-insert comment markers (`<!-- wp:paragraph -->` in WordPress, for example) to track which block of a visual editor produced which markup.
- Developers commonly comment out an entire section of a page mid-build to isolate a rendering or CSS bug, without losing the markup while testing.
- Long, non-obvious layout hacks (a spacer div, a specific `z-index` workaround) are often paired with a comment explaining *why* the odd-looking markup exists, saving the next person from "fixing" it and breaking something.
- Browser extensions and automated scrapers sometimes look for comment markers as informal, lightweight metadata boundaries in scraped HTML.

---

## ⚖️ Advantages

- Zero effect on page rendering or layout — a comment is completely invisible to a normal visitor.
- Works anywhere in the document (inside `<head>`, `<body>`, even inside most elements) and can wrap large blocks of markup at once.
- Extremely lightweight way to leave context for future maintainers directly next to the code it explains, rather than in a separate document that can drift out of sync.
- Useful, low-risk way to temporarily disable markup during debugging without the destructive step of deleting it.

---

## ⚠️ Limitations

- Cannot be nested, which limits their use for "commenting out a section that already contains a comment" — the outer comment closes early at the first `-->` encountered.
- Comments add to the page's total byte size sent over the network, however negligibly — large, generated comment blocks left in production do carry a small real cost.
- They are not a substitute for proper documentation systems, commit messages, or code review notes — a comment buried in markup is easy to miss compared to a well-organized changelog or README.

---

## 🚨 Common Mistakes

- Leaving sensitive information — API keys, internal URLs, admin notes, "known bug, don't tell the client" — in a comment, forgetting it ships to every visitor's browser.
- Attempting to nest comments to comment out a block that already contains a comment, not realizing the outer comment silently closes early.
- Leaving large amounts of dead, commented-out markup in production for months, cluttering the file and confusing future readers about whether it's meant to come back.
- Using comments as a substitute for meaningful class names or semantic structure, instead of actually organizing the markup clearly.
- Forgetting that `-->` inside what you intended as comment text (for example, inside a code sample being shown inside a comment) will prematurely close the comment.

---

## 📖 Further Reading

- MDN: "HTML comments"
- W3C HTML specification: comment syntax in the "Syntax" section

---

## 💡 Wisdom from Mímir

I explain HTML comments to newer developers with one sentence: **"View Source is one click away for anyone."** Once that clicks, the instinct to jot a stray password or internal note in a comment just to save five minutes disappears on its own — and what's left is exactly what comments are good for: honest, useful context for the next developer, written where they'll actually see it.

---

## 🔗 Related Notes

- [[Semantic HTML and Why It Matters]] — comments explain structure; semantics *are* the structure
- [[Character Encoding and HTML Entities]] — another place where "what's actually sent to the browser" matters more than what's rendered
