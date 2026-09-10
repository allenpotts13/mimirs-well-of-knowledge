---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: HTML
difficulty: Intermediate
tags:
  - images
  - responsive-design
  - performance
  - accessibility
aliases:
  - Responsive Images
  - srcset and sizes
  - picture element
publish: true
permalink: html/images-and-responsive-images-srcset-and-picture
---

# <span class="rune">ᛟ</span> Images and Responsive Images (srcset and picture)

> *A single `<img>` tag can only ever serve one file — `srcset`, `sizes`, and `<picture>` exist to let the browser pick the right file, or the right crop, for the device actually looking at it.*

---

## 🎯 Purpose

The naive `<img src="photo.jpg">` forces every visitor — a phone on a slow connection and a 4K monitor on fiber alike — to download the exact same file. That's either blurry on the big screen or wasteful on the small one. Responsive images solve two genuinely different problems that get conflated: **serving a smaller version of the same picture** for performance (resolution switching), and **serving a genuinely different crop or composition** at different sizes so the image still reads well (art direction). HTML has a dedicated tool for each.

---

## 🧠 Key Ideas

- `alt` is not optional decoration — it's the text a screen reader announces instead of the image, and what displays if the image fails to load.
- A **meaningful** image (a diagram, a product photo, a chart) needs descriptive `alt` text; a **decorative** image (a background flourish, a spacer, a repeated icon next to text that already says the same thing) needs `alt=""` so assistive tech skips it silently instead of announcing something useless like "image1234.jpg."
- `srcset` + `sizes` on a plain `<img>` handles **resolution switching**: same image, multiple file sizes, browser picks the best fit for the viewport and pixel density.
- `<picture>` with multiple `<source>` elements handles **art direction**: genuinely different images (different crops, aspect ratios, or even different pictures) shown at different breakpoints.
- `<picture>` always needs a fallback `<img>` as its last child — that's what actually renders; the `<source>` elements only ever *suggest* alternatives to it.

---

## ⚙️ How It Works

Think of `srcset` as giving the browser a menu of file sizes and letting it order for you, based on things it knows and you don't at write-time — the viewport width, the device pixel density, the user's data-saver setting. You describe *candidates*; the browser makes the *decision*. `sizes` is what tells the browser how much space the image will actually occupy in the layout at different viewport widths, so it can match that against the `srcset` candidates and pick intelligently — without `sizes`, the browser has to guess the image will be full-viewport width, which is wrong for anything in a grid or sidebar.

`<picture>` flips the control: instead of the browser optimizing within one image, *you* declare different images for different conditions via `media` queries on `<source>`, and the browser just picks the first matching source top-to-bottom. Use `srcset`/`sizes` when it's the same picture at different sizes. Use `<picture>` when the picture itself should change — e.g., a tall, tightly-cropped portrait on mobile versus a wide landscape shot on desktop, where simply shrinking the desktop image would leave a subject too small to see on a phone.

---

## 💻 Examples

```html
<!-- Decorative image: purely visual, adds no information -->
<img src="divider-flourish.png" alt="">

<!-- Meaningful image: needs a real description -->
<img src="quarterly-sales-chart.png"
     alt="Bar chart showing sales rising from $2M in Q1 to $5M in Q4">
```

```html
<!-- Resolution switching: same photo, several file sizes -->
<img
  src="hero-800.jpg"
  srcset="
    hero-400.jpg   400w,
    hero-800.jpg   800w,
    hero-1200.jpg 1200w,
    hero-1600.jpg 1600w
  "
  sizes="(max-width: 600px) 100vw, (max-width: 1000px) 50vw, 800px"
  alt="Hikers crossing a ridge at sunrise">
<!-- sizes tells the browser: below 600px viewport, the image fills the screen;
     below 1000px, it's about half the viewport; otherwise it's fixed at 800px.
     The browser matches that against srcset candidates and its own pixel density
     to download only the file it actually needs. -->
```

```html
<!-- Art direction: a genuinely different crop per breakpoint -->
<picture>
  <!-- Tight vertical crop for small screens -->
  <source media="(max-width: 600px)" srcset="portrait-crop.jpg">
  <!-- Wide crop for tablets -->
  <source media="(max-width: 1000px)" srcset="square-crop.jpg">
  <!-- Fallback: also the default for anything not matched above -->
  <img src="landscape-crop.jpg" alt="Team celebrating on stage after the product launch">
</picture>
```

```html
<!-- Art direction + format fallback combined: modern format first, then JPEG -->
<picture>
  <source type="image/avif" srcset="photo.avif">
  <source type="image/webp" srcset="photo.webp">
  <img src="photo.jpg" alt="Close-up of hand-thrown ceramic mug on a wheel">
</picture>
```

---

## 🚀 Real World Applications

- News and e-commerce sites serve smaller hero images to mobile visitors, cutting page weight and improving load time on cellular connections.
- Marketing pages art-direct a hero banner so the subject stays legible — a wide establishing shot on desktop, a tight crop on the subject's face on mobile.
- Modern image CDNs (Cloudinary, Imgix, Next.js Image) generate `srcset` candidates automatically from one source file uploaded once.
- `alt=""` on decorative icons next to labeled buttons (e.g. a trash icon beside the word "Delete") avoids screen readers announcing redundant noise like "trash can icon, Delete."
- Format-fallback `<picture>` blocks let sites ship AVIF/WebP to modern browsers for smaller file sizes while still supporting older browsers via JPEG/PNG.

---

## ⚖️ Advantages

- Real, measurable performance wins — mobile users don't download desktop-sized files.
- Art direction keeps images actually legible and well-composed across wildly different aspect ratios, instead of just shrinking one image badly.
- Good `alt` text makes images useful (or invisible, when appropriate) to screen reader users instead of being a wall of noise or a dead end.
- Declarative — no JavaScript needed; the browser does the size/format selection natively.

---

## ⚠️ Limitations

- `srcset`/`sizes` syntax is fussy and easy to get subtly wrong (missing `w` descriptors, a `sizes` that doesn't match your actual CSS layout), which silently degrades the optimization rather than erroring loudly.
- `<picture>` requires maintaining multiple actual image files/crops, which is real ongoing production overhead for content teams.
- Neither mechanism helps if the underlying image itself is unoptimized (wrong compression, huge dimensions) — they choose *among* files you provide, they don't compress anything.
- Testing across many breakpoints and pixel densities is genuinely tedious without automated tooling.

---

## 🚨 Common Mistakes

- Leaving `alt` off entirely rather than using `alt=""` for decorative images — a missing `alt` attribute is announced by screen readers as the filename or "image," which is worse than nothing.
- Writing `alt` text that just repeats "image of..." or restates visible caption text verbatim instead of adding real information.
- Using `srcset` without `sizes`, so the browser falls back to assuming the image is full-viewport width and picks a needlessly large file.
- Reaching for `<picture>` when plain `srcset`/`sizes` would do — if it's the same image just resized, `<picture>` is unnecessary complexity.
- Forgetting the fallback `<img>` inside `<picture>`, or giving it a smaller/lower-quality image than intended, since it's also what renders for browsers with no source match.

---

## 📖 Further Reading

- MDN: "Responsive images"
- MDN: `<picture>` element reference
- web.dev: "Serve responsive images"

---

## 💡 Wisdom from Mímir

The question I ask myself before writing `srcset` or `<picture>` is simple: *is this the same picture, or a different one?* Same picture at different sizes is a performance problem — `srcset`/`sizes` solves it. A genuinely different crop or composition is a design problem — `<picture>` solves that. Reaching for the wrong tool doesn't break anything, but it does mean maintaining art-direction-level complexity for a problem that was really just about file size, or vice versa settling for one crop that looks wrong at every breakpoint but one.

---

## 🔗 Related Notes

- [[Semantic HTML and Why It Matters]] — the same "meaning over appearance" principle applies to `alt` text
- [[Accessibility Basics - alt Text, Labels, and ARIA]] — deeper treatment of writing good alternative text
- [[Embedding Media - audio, video, and iframe]] — `loading="lazy"` applies to images too, and pairs naturally with responsive images
