---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: CSS
difficulty: Intermediate
tags:
  - font-face
  - typography
  - web-fonts
  - performance
aliases:
  - "@font-face"
  - Web Fonts
  - font-display
publish: true
permalink: css/custom-fonts-with-font-face
---

# <span class="rune">ᛟ</span> Custom Fonts with @font-face

> *`@font-face` teaches the browser about a font it doesn't already have installed, and `font-display` decides what happens to your text during the awkward gap before that font file finishes downloading.*

---

## 🎯 Purpose

Browsers ship with a small set of default fonts, and design rarely stops there — brand identity, readability, and personality all depend on typography that a user's operating system almost certainly doesn't already have installed. `@font-face` is the rule that lets a website say "here is a font, here's where to download its file, and here's the name to refer to it by in `font-family`" — turning any font file you have the license to use into a first-class citizen of your CSS, without requiring the visitor to have it pre-installed.

---

## 🧠 Key Ideas

- `@font-face` declares a custom font: it gives the font a `font-family` name, points to one or more font **file sources**, and can specify `font-weight`/`font-style` so multiple files (regular, bold, italic) map to the same family name under different variants.
- The browser downloads the font file over the network, which means there's an unavoidable gap between "page text is ready to render" and "the actual custom font has arrived" — `font-display` controls what the user sees during that gap.
- `font-display: swap` renders text immediately in a fallback font, then swaps to the custom font once it loads — prioritizing readability, at the cost of a visible font swap (sometimes called FOUT, Flash of Unstyled Text).
- `font-display: block` briefly hides text (usually up to ~3 seconds) waiting for the custom font before falling back — prioritizing final visual polish, at the cost of a brief invisible-text period (FOIT, Flash of Invisible Text).
- Providing multiple `format()` hints inside `src` (e.g. `woff2`, `woff`) lets the browser pick the first format it supports without downloading formats it can't use — modern practice is usually just `woff2` alone, since browser support is now nearly universal.

---

## ⚙️ How It Works

Think of `@font-face` as registering a font with the browser the same way you'd introduce a new hire to a company directory — "this is Montserrat Bold, you can reach it at this file path, and from now on anyone asking for `font-family: Montserrat` with `font-weight: 700` should be routed to it." Once declared, you use that font family name in ordinary `font-family` rules exactly as if it had shipped with the browser.

The catch is that unlike a system font, a custom font is a network resource — it has to be requested and downloaded before it can be used to paint text, and that takes time, especially on a slow connection. Browsers have to make a decision about what to display in that window between "the text is otherwise ready" and "the font file has actually arrived," and `font-display` is how you tell them which trade-off you prefer.

- `swap` says: don't make the user wait — show the text immediately using a fallback font (something in the `font-family` stack after the custom font, or the browser default), then swap it out for the custom font the moment it's ready. The user reads content sooner, at the cost of a visible "jump" when the fonts swap (a layout shift if the fallback and custom font have different letter widths).
- `block` says: the custom font is important enough to the design that showing the wrong font briefly isn't acceptable — hide the text (a short, capped invisible period) and wait for the real font, falling back only if it takes unusually long. The user might briefly see nothing where text should be, but never sees the "wrong" typeface.

There's no universally correct choice — it's a genuine trade-off between "get content in front of the user fast" (`swap`, generally the safer default for body text and performance-sensitive pages) and "never show the wrong typeface" (`block`, sometimes chosen for logotype-heavy branding where a flash of the wrong font looks jarring).

`format()` hints exist because font files come in several container formats (`woff2`, `woff`, historically `ttf`, `eot`, `svg` for very old browser support), and listing multiple `src` entries with format hints lets the browser skip formats it doesn't support without wasting a download attempt on them. In current practice, `woff2` alone covers essentially all browsers in active use, so many modern stylesheets have simplified back down to a single source.

---

## 💻 Examples

```css
/* Declaring a custom font family, with format hints and font-display */
@font-face {
  font-family: "Inter";
  src: url("/fonts/inter-regular.woff2") format("woff2"),
       url("/fonts/inter-regular.woff") format("woff"); /* fallback for older browsers */
  font-weight: 400;
  font-style: normal;
  font-display: swap; /* show fallback text immediately, swap in once loaded */
}

@font-face {
  font-family: "Inter";
  src: url("/fonts/inter-bold.woff2") format("woff2");
  font-weight: 700; /* same family name, different weight — mapped to a different file */
  font-style: normal;
  font-display: swap;
}
```

```css
/* Using the declared font, with a sensible fallback stack in case
   the custom font fails to load or is still loading */
body {
  font-family: "Inter", -apple-system, "Segoe UI", Roboto, sans-serif;
}

h1, h2, h3 {
  font-weight: 700; /* automatically resolves to inter-bold.woff2 above */
}
```

```css
/* font-display: block — used sparingly, e.g. for a distinctive
   logotype/wordmark where showing the wrong font briefly looks bad */
@font-face {
  font-family: "BrandDisplay";
  src: url("/fonts/brand-display.woff2") format("woff2");
  font-display: block; /* hide briefly rather than show a fallback */
}
```

```html
<!-- Self-hosting: preloading the primary font file speeds up the
     swap by starting the download before the CSS is even parsed -->
<link rel="preload" href="/fonts/inter-regular.woff2" as="font"
      type="font/woff2" crossorigin>
```

---

## 🚀 Real World Applications

- Loading a brand's chosen typeface (from Google Fonts, Adobe Fonts, or a purchased license) for a marketing site or product UI where the default system font doesn't match the design.
- Choosing `font-display: swap` on body copy for a content-heavy site (blog, documentation) where getting words on screen fast matters more than avoiding a brief font swap.
- Self-hosting fonts to avoid a third-party network request (to Google Fonts' CDN, for example) for privacy, performance, or reliability reasons — some regions/networks block or slow third-party font CDNs.
- Declaring separate `@font-face` blocks per weight/style (regular, bold, italic, bold-italic) so `font-weight`/`font-style` in ordinary CSS "just work" without loading a single oversized variable font file unnecessarily.

---

## ⚖️ Advantages

- Full typographic control independent of what fonts happen to be installed on a visitor's device — a huge upgrade over the pre-web-fonts era of "web-safe fonts only."
- `font-display` gives explicit, deliberate control over the loading trade-off instead of leaving it to inconsistent browser defaults.
- Self-hosting fonts (vs. a third-party service) avoids an extra DNS lookup/connection to another domain, which can measurably improve load performance.
- Modern `woff2` compression is significantly smaller than older font formats, keeping the performance cost of custom fonts fairly low when used deliberately.

---

## ⚠️ Limitations

- Every custom font is an additional network request and render-blocking risk if not handled carefully — heavy, multi-weight font loading can noticeably hurt page performance.
- `font-display: swap`'s visible font swap can cause layout shift (text reflows as letter widths change), which can hurt Cumulative Layout Shift metrics if the fallback and custom fonts aren't sized similarly.
- `font-display: block`'s invisible-text window, while capped, still means some users on slow connections briefly see nothing at all where text should be.
- Self-hosting requires you to actually manage font files, licensing, and updates yourself — a web font service (Google Fonts, Adobe Fonts) handles hosting, format generation, and updates for you, at the cost of an external dependency and a third-party network request.

---

## 🚨 Common Mistakes

- Loading every weight and style of a font family (thin, light, regular, medium, semibold, bold, black × italic) when a design only actually uses two or three — needlessly bloating page weight.
- Forgetting `font-display` entirely and getting the browser's default behavior (historically closer to `block`/`auto`, varying by browser), rather than a deliberate choice.
- Not providing a reasonable fallback font stack after the custom font name — if the custom font fails to load entirely, text falls back to the browser default, which may look jarring without a deliberate intermediate fallback.
- Using `font-weight: 700` on an element without ever having declared an `@font-face` for that weight — the browser will "fake bold" by synthetically distorting the regular weight, which typically looks worse than a real bold font file.
- Loading fonts from a third-party CDN without considering `preconnect`/`preload`, missing an easy performance win, or conversely self-hosting without ever setting caching headers correctly on the font files.

---

## 📖 Further Reading

- MDN: "@font-face"
- MDN: "font-display"
- web.dev: "Best practices for fonts"
- Google Fonts / Adobe Fonts documentation — for the web-font-service side of the self-host vs. service trade-off

---

## 💡 Wisdom from Mímir

The `font-display` decision is one of the few places in CSS where you're explicitly choosing which flavor of imperfection your users experience — there's no option that avoids the loading gap entirely, only different ways of spending it. My default is `swap` almost everywhere, because readable text in the "wrong" font beats no text at all, and I reserve `block` for the rare case where a specific typeface really is part of the content itself (a wordmark, a logotype) rather than just a nicety. And I've come to prefer self-hosting over a third-party font CDN by default now — it's one less external domain in the critical rendering path, and one less thing that breaks when someone else's CDN has a bad day.

---

## 🔗 Related Notes

- [[CSS Units - px, em, rem, %, and vw-vh]] — font sizing units work alongside custom fonts to build a full typographic scale
- [[CSS Variables (Custom Properties)]] — commonly used to define a project's font-family stack once and reuse it consistently
- [[Responsive Design with Media Queries]] — font sizes and loading strategy sometimes vary by breakpoint (e.g. lighter font loading on smaller/mobile viewports)
