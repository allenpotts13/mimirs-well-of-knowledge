---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: HTML
difficulty: Beginner
tags:
  - links
  - accessibility
  - security
  - fundamentals
aliases:
  - Anchor Tag Best Practices
  - a href
publish: true
---

# <span class="rune">ᛟ</span> Links and Navigation Best Practices

> *A link's text has to make sense on its own, out of context, because that's often exactly how it gets encountered — and where a link sends you, and how much it exposes about where you came from, are both choices you're making whether you realize it or not.*

---

## 🎯 Purpose

The `<a>` tag looks like the simplest element in HTML — wrap some text, add an `href`, done. But three easy-to-miss decisions around it have outsized real-world consequences: whether the link text alone communicates its destination, whether opening a link in a new tab accidentally hands that new page a live reference back to your own window, and whether a URL should be relative or absolute. This concept exists because links are the connective tissue of the entire web, and getting these details wrong quietly degrades accessibility, security, and maintainability all at once.

---

## 🧠 Key Ideas

- Write **meaningful link text** that makes sense standalone — never "click here" or "read more" with no other context.
- Screen reader users frequently pull up a **list of all links on the page**, completely divorced from their surrounding paragraph text, as a way to skim available destinations — link text that only makes sense "in context" fails this use case entirely.
- `target="_blank"` opens a link in a new tab/window — but without `rel="noopener"`, the newly opened page gets a live `window.opener` reference back to the page that opened it, which is a real security and performance risk.
- `rel="noopener"` severs that connection; `rel="noreferrer"` does the same and additionally suppresses the `Referer` header, hiding where the click came from.
- **Relative URLs** (`/about`, `../images/logo.png`) are resolved against the current page's location, ideal for links within your own site; **absolute URLs** (`https://example.com/about`) always point to the same place regardless of where the link lives, necessary for linking to external sites.

---

## ⚙️ How It Works

Imagine a screen reader user pulling up their browser's "list all links on this page" feature — a real, commonly used feature in every major screen reader, functionally similar to how a sighted user might scan a page for blue underlined text. If every link on the page just says "click here" or "read more," that list is useless: a wall of identical, meaningless entries with no way to tell them apart. If instead each link says "Read our refund policy" or "Download the 2026 tax guide (PDF)," that same list becomes a legitimate, navigable menu of destinations. The lesson generalizes beyond screen readers too — meaningful link text also helps SEO (search engines use link text as a ranking signal for the linked page) and simply helps any reader scanning quickly.

`target="_blank"` has a subtler problem. When a link with `target="_blank"` is clicked, the newly opened tab receives a JavaScript object, `window.opener`, that is a live reference back to the *original* tab — meaning the new page (which could be malicious, or simply poorly coded) can run `window.opener.location = 'https://phishing-site.com'`, silently redirecting your original tab to somewhere else entirely, even though the user never clicked anything on that tab. Adding `rel="noopener"` (browsers have made this the default behavior for `target="_blank"` links in recent years, but it's still best practice to be explicit) sets `window.opener` to `null` in the new tab, closing that hole. `rel="noreferrer"` does the same thing and additionally strips the `Referer` header the browser would otherwise send, which is useful when you don't want the destination site to know exactly which page or article linked them there.

Relative vs. absolute URLs is a simpler distinction but a common source of broken links: a relative URL like `/blog/post-1` is resolved *relative to the current page's own location*, which makes it robust across environments (it works identically on `localhost`, a staging domain, and production, since it never hardcodes a domain) — but it will break entirely if used to link to a page that isn't actually part of the same site. An absolute URL like `https://example.com/blog/post-1` always resolves to the same destination no matter where the link is placed, which is required for any link pointing off-site, but becomes a maintenance liability if hardcoded for pages that are actually part of your own site (e.g. it won't correctly follow you from a staging environment to production).

---

## 💻 Examples

```html
<!-- WRONG: meaningless out-of-context link text -->
<p>We updated our privacy policy. <a href="/privacy">Click here</a> to read it.</p>

<!-- RIGHT: the link text alone communicates the destination -->
<p>We updated our <a href="/privacy">privacy policy</a>.</p>
```

```html
<!-- WRONG: target="_blank" without rel, leaves window.opener exposed -->
<a href="https://external-site.com" target="_blank">Visit their site</a>

<!-- RIGHT: noopener closes the window.opener security hole -->
<a href="https://external-site.com" target="_blank" rel="noopener">Visit their site</a>

<!-- Also hides the referrer, if you don't want the destination to know where the click came from -->
<a href="https://external-site.com" target="_blank" rel="noopener noreferrer">Visit their site</a>
```

```html
<!-- Relative URL: resolved against the current page, ideal for same-site links -->
<a href="/about">About Us</a>
<a href="../images/logo.png">Logo</a>

<!-- Absolute URL: always points to the same place, required for external links -->
<a href="https://developer.mozilla.org/en-US/docs/Web/HTML">MDN HTML Docs</a>
```

```html
<!-- A link inside a list, as a screen reader's "list all links" feature would encounter it -->
<nav>
  <ul>
    <li><a href="/products">Browse Products</a></li>
    <li><a href="/support/contact">Contact Support</a></li>
    <li><a href="/orders/track">Track Your Order</a></li>
  </ul>
</nav>
<!-- Each link makes sense read completely on its own, with zero surrounding context -->
```

---

## 🚀 Real World Applications

- News and blog sites routinely get flagged in accessibility audits for "read more" links repeated across an entire article listing page, with no distinguishing text for screen reader users skimming by link.
- Any site linking to user-submitted or third-party URLs in a new tab (forums, comment sections, social platforms) should use `rel="noopener noreferrer"` as a baseline defense against the linked page manipulating the referring tab.
- Large sites migrating domains or moving between staging/production environments benefit heavily from relative URLs for internal navigation, since absolute internal links would all need rewriting.
- SEO audits check link text as a ranking factor — "click here" links are considered a wasted opportunity to signal what the destination page is about via anchor text.
- Marketing and affiliate links intentionally use `rel="noreferrer"` (or `rel="sponsored"`/`rel="nofollow"` per search engine guidelines) to control what information and ranking signal is passed to the destination.

---

## ⚖️ Advantages

- Meaningful link text improves accessibility, SEO, and general scannability simultaneously, for zero extra markup cost — it's a wording choice, not a technical one.
- `rel="noopener"` closes a genuine, exploitable security gap with a single attribute and no functional downside for legitimate use cases.
- Relative URLs make internal navigation resilient to domain and environment changes, reducing link-rot risk during migrations.

---

## ⚠️ Limitations

- Writing genuinely descriptive link text takes more thought and slightly more words than "click here" — a small but real authoring cost.
- `rel="noopener"` is now the browser default for `target="_blank"` links in most modern browsers, which can make it easy to assume it's unnecessary — but explicit is still safer, since defaults can vary by browser and version, and older or embedded browser contexts may not apply it.
- Relative URLs can break if a page is moved to a different relative depth in the site structure without updating its links, whereas absolute URLs (though less flexible) don't have this particular failure mode.

---

## 🚨 Common Mistakes

- Using "click here," "read more," or "this link" as the entirety of a link's visible text.
- Opening third-party or user-generated links in a new tab without `rel="noopener"`, leaving `window.opener` exposed to the destination page.
- Using an absolute URL (with full domain) for internal links, causing broken navigation when the site moves between environments (localhost, staging, production) or domains.
- Wrapping an image in a link without accessible text describing the destination (missing `alt` text on the image, and no visible text alternative either), leaving the link with no announced purpose at all.
- Using `<a>` for something that isn't actually a navigation action (like a JavaScript-triggered UI toggle) instead of using a `<button>`, misusing the element's semantic meaning.

---

## 📖 Further Reading

- MDN: "`<a>`: The Anchor element"
- MDN: "Types of link relation" (`rel` attribute values including `noopener` and `noreferrer`)
- WebAIM: "Links and Hypertext" (link text accessibility guidance)

---

## 💡 Wisdom from Mímir

I read my own link text back to myself with the surrounding sentence deleted — just the words inside the `<a>` tag, alone, as if that's all a reader will ever see. If it doesn't tell me where I'm going, I rewrite it. That one habit has caught more bad link text in my own work than any linter ever has.

---

## 🔗 Related Notes

- [[Buttons vs Links - button vs a]] — the companion decision of *when* something should be a link at all versus a button
- [[Semantic HTML and Why It Matters]] — the broader principle that an element's meaning should match its actual purpose
- [[Accessibility Basics - alt Text, Labels, and ARIA]] — how screen readers surface links as a standalone navigation mechanism
