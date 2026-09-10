---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: HTML
difficulty: Beginner
tags:
  - javascript
  - attributes
  - fundamentals
aliases:
  - "Data Attributes (data-*)"
  - data-* Attributes
  - dataset
  - Custom Data Attributes
publish: true
---

# <span class="rune">ᛟ</span> Data Attributes (data-*)

> *`data-*` attributes are the one standards-blessed way to stash custom information on an HTML element — a valid alternative to inventing an attribute HTML never agreed to.*

---

## 🎯 Purpose

JavaScript frequently needs to attach a bit of extra information to an element that isn't covered by any standard HTML attribute — a database ID, a state flag, a config value a widget reads on init. Before `data-*` existed, developers either abused existing attributes for unintended purposes or invented made-up ones (`<div userid="42">`), both of which are invalid HTML and not future-proof — a browser vendor could add a real `userid` attribute tomorrow with entirely different behavior. The `data-*` prefix exists as a permanently reserved, guaranteed-safe namespace: anything starting with `data-` is validated HTML and guaranteed to never collide with a future standard attribute.

---

## 🧠 Key Ideas

- Any attribute named `data-*` (e.g. `data-user-id`, `data-status`, `data-max-items`) is valid HTML by specification — this is the sanctioned way to attach custom data to an element, unlike a made-up attribute name.
- In JavaScript, `element.dataset.someName` reads and writes the corresponding `data-some-name` attribute — the browser automatically converts between kebab-case in HTML and camelCase in JS.
- `dataset` values are always strings, even if they look like numbers or booleans — `data-count="5"` comes back as `"5"`, not `5`, so conversion is on you.
- `data-*` is for data with **no defined meaning HTML already has a slot for** — data that's genuinely custom to your application or script, not a substitute for real semantic attributes that already exist for that purpose.
- CSS can also read `data-*` values via attribute selectors (`[data-status="active"]`) or `attr()` in generated content, making them useful as CSS hooks too, not just a JS mechanism.

---

## ⚙️ How It Works

Every `data-*` attribute you write in HTML shows up automatically on that element's `.dataset` object in JavaScript, with the name transformed from `kebab-case` (HTML's convention) to `camelCase` (JavaScript's convention) — `data-user-id` becomes `dataset.userId`, `data-max-retry-count` becomes `dataset.maxRetryCount`. This is a live, two-way mapping: setting `element.dataset.status = 'done'` in JS writes the actual `data-status="done"` attribute back onto the element, visible in the DOM and to CSS selectors.

The judgment call is knowing when `data-*` is the *right* tool versus a lazy one. If HTML already has a real attribute for what you're storing — an image's alt text, an input's value, a link's destination — use that real attribute; it carries actual meaning to browsers and assistive tech that a `data-*` attribute never will (a screen reader has no idea what `data-status="urgent"` means, but it absolutely knows what `aria-label` or `alt` mean). `data-*` is for the leftover category: information with no standard home, meaningful only to your own CSS or JavaScript.

---

## 💻 Examples

```html
<!-- Custom data for a JS widget to read on init -->
<div id="carousel" data-autoplay="true" data-interval="4000" data-max-slides="6">
  ...
</div>

<script>
  const carousel = document.getElementById('carousel');
  console.log(carousel.dataset.autoplay);   // "true"  (string, not boolean!)
  console.log(carousel.dataset.interval);   // "4000"  (string, not number!)

  // Convert deliberately -- dataset never does this for you
  const interval = Number(carousel.dataset.interval);
  const autoplay = carousel.dataset.autoplay === 'true';
</script>
```

```html
<!-- Storing an app-specific ID with no standard HTML attribute for it -->
<li class="task" data-task-id="8291" data-status="in-progress">
  Finish the quarterly report
</li>

<script>
  document.querySelectorAll('.task').forEach(task => {
    task.addEventListener('click', () => {
      // Read the ID to know which task this row represents
      fetch(`/api/tasks/${task.dataset.taskId}`);
    });
  });
</script>
```

```css
/* CSS can read data-* directly, no JS needed */
.task[data-status="done"] {
  text-decoration: line-through;
  opacity: 0.6;
}
```

```html
<!-- Wrong tool: don't use data-* when a real attribute already exists -->
<img src="cat.jpg" data-description="A gray cat sleeping on a windowsill">
<!-- Should be: -->
<img src="cat.jpg" alt="A gray cat sleeping on a windowsill">
<!-- alt is the real, meaningful attribute here -- a screen reader reads it;
     data-description is invisible to assistive tech entirely -->
```

---

## 🚀 Real World Applications

- JavaScript UI libraries (Bootstrap components, custom carousels/modals/tooltips) configure widget behavior declaratively via `data-*` attributes read on initialization, instead of requiring a separate JS config object.
- Analytics tracking frequently tags elements with `data-analytics-id` or `data-track-event` so click-tracking scripts know what was interacted with without parsing visible text.
- Test automation suites often rely on dedicated `data-testid` attributes specifically so tests can target elements independent of CSS classes or visible text, which change more often.
- E-commerce product listings tag each product card with `data-product-id`, `data-price`, or `data-in-stock` so filtering/sorting JavaScript can act on structured values without re-parsing the DOM's visible text.
- Single-page app frameworks and vanilla JS alike use `data-*` as a lightweight way to pass server-rendered values into client-side JavaScript without a separate API call.

---

## ⚖️ Advantages

- Fully valid, standards-compliant HTML — unlike invented attribute names, guaranteed to never collide with a future real attribute.
- Simple, native JS access via `.dataset` with no library needed.
- Also queryable and stylable directly from CSS via attribute selectors.
- Keeps custom/application-specific data visibly attached to the relevant element in markup, rather than in a separate, harder-to-sync JS data structure.

---

## ⚠️ Limitations

- Values are always strings — numbers, booleans, and structured data all require manual parsing/conversion on the way out.
- Not meaningful to assistive technology or search engines at all — using `data-*` where a semantic or ARIA attribute exists throws away real functionality for no benefit.
- Storing large or complex data (e.g. a big JSON blob) in a `data-*` attribute bloats the HTML and is usually a sign the data belongs in a JS variable or a fetched API response instead.
- Attribute names are case-insensitive in HTML, which imposes constraints on `dataset` naming — `data-tests-ID` doesn't map cleanly and should be avoided in favor of straightforward lowercase kebab-case.

---

## 🚨 Common Mistakes

- Using `data-*` for information that already has a proper semantic or ARIA attribute (`alt`, `title`, `aria-label`, `href`) instead of using the real one.
- Forgetting `dataset` values are strings and comparing them directly to numbers or booleans without conversion (`if (el.dataset.active)` is true for the *string* `"false"` too, since any non-empty string is truthy).
- Inventing a non-`data-` custom attribute (`<div userid="42">`) instead of using the reserved, valid `data-*` namespace.
- Overloading a single `data-*` attribute with a delimited string of multiple values instead of using several separate, clearly named `data-*` attributes.
- Storing sensitive data in `data-*` attributes, forgetting that anything in the HTML source is visible to anyone who views page source — never a substitute for real server-side access control.

---

## 📖 Further Reading

- MDN: "Using data attributes"
- MDN: `HTMLElement.dataset` reference
- MDN: attribute selectors (`[attr=value]`) in CSS

---

## 💡 Wisdom from Mímir

The test I use for `data-*` is whether I can name a *real* HTML or ARIA attribute this data should have been instead — if I can, I use that one, because it comes with meaning built in that `data-*` never provides. If I genuinely can't (an internal ID, a widget config flag, a test hook), that's exactly the leftover category `data-*` was designed for, and I reach for it without a second thought.

---

## 🔗 Related Notes

- [[Semantic HTML and Why It Matters]] — the general principle of preferring meaningful attributes over generic ones
- [[Accessibility Basics - alt Text, Labels, and ARIA]] — the real semantic attributes `data-*` should never substitute for
