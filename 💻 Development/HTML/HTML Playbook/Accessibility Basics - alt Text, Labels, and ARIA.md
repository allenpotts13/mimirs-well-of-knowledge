---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: HTML
difficulty: Beginner
tags:
  - accessibility
  - aria
  - forms
  - fundamentals
aliases:
  - "Accessibility Basics: alt Text, Labels, and ARIA"
  - a11y Basics
  - ARIA vs Semantic HTML
publish: true
---

# 📚 Accessibility Basics: alt Text, Labels, and ARIA

> *Accessibility starts with choosing the right native element — ARIA is a patch for the gaps that are left over, not a substitute for doing that first.*

---

## 🎯 Purpose

Accessible markup exists so that people using screen readers, keyboard-only navigation, voice control, or magnification can actually use what you build — not as an afterthought bolted on at the end, but as a property of choosing the right elements and attributes from the start. Most of accessibility isn't exotic: it's `alt` text that actually describes the image, form inputs correctly paired with their labels, and native interactive elements used for interactive things. ARIA exists to cover the remaining gap — custom widgets and dynamic behavior that plain HTML has no vocabulary for — and it only works well when it's reserved for that gap.

---

## 🧠 Key Ideas

- **"No ARIA is better than bad ARIA"** — a real `<button>` is keyboard-focusable, triggers on Enter and Space, and is announced correctly, all automatically. A `<div role="button">` gets none of that for free; you must manually wire up `tabindex`, keydown handlers for both Enter and Space, and focus styles, and it's extremely easy to get subtly wrong.
- `<label for="input-id">` (or wrapping the input directly) creates a programmatic association a screen reader announces when the input receives focus — without it, a sighted user sees a label near a field, but a screen reader user hears only "edit text," with no idea what to type.
- ARIA is a **supplement**, not a replacement — it adds semantics HTML has no native element for (custom dropdowns, live regions, tab panels), but it never grants behavior the way native elements do. Adding `role="button"` doesn't make something keyboard-operable; you still have to build that yourself.
- Every ARIA attribute you add is a promise you're making to assistive tech about how the element behaves — a promise the browser can't verify and won't help you keep.
- The **first rule of ARIA use** (from the W3C ARIA Authoring Practices): if a native HTML element or attribute has the semantics and behavior you need, use it instead of repurposing an element and adding ARIA to make it accessible.

---

## ⚙️ How It Works

Every element in the DOM has a corresponding node in the browser's **accessibility tree** — a parallel structure that screen readers and other assistive tech actually read from, built from tag semantics, ARIA attributes, and computed properties like the accessible name. Native elements populate that tree correctly by default: a `<button>` reports role "button," a name (from its text content), and a state (enabled/disabled/pressed). A generic `<div>` reports as a generic container with none of that — styling it to *look* like a button changes nothing in the accessibility tree.

ARIA attributes (`role`, `aria-label`, `aria-expanded`, `aria-live`, and dozens more) let you write directly into that tree by hand, for the cases where no native element covers what you're building — a combobox, a tab interface, a live status region announcing "3 items added to cart." But ARIA only changes what's *announced* — it never changes actual keyboard behavior, focus order, or event handling, all of which you must implement yourself with JavaScript when you reach for a custom widget. That's the whole reason "no ARIA is better than bad ARIA" is true: a bare `<div role="button">` with a `click` handler but no keydown handler *announces* itself as a button, promising keyboard operability it doesn't deliver — actively misleading a screen reader user who then can't Tab to it or press Enter to activate it. Silence (no role at all) would at least not have made a false promise.

---

## 💻 Examples

```html
<!-- Correctly associated label and input -->
<label for="email">Email address</label>
<input type="email" id="email" name="email">
<!-- Clicking the label focuses the input; a screen reader announces
     "Email address, edit text" when the input receives focus -->
```

```html
<!-- Also valid: wrapping instead of matching id/for -->
<label>
  Email address
  <input type="email" name="email">
</label>
```

```html
<!-- Bad: a div masquerading as a button -->
<div class="btn" onclick="save()">Save</div>
<!-- Not focusable, not announced as a button, doesn't fire on Enter/Space -->

<!-- Better: just use a button -->
<button type="button" onclick="save()">Save</button>
<!-- Focusable, announced as "Save, button", works with Enter/Space for free -->

<!-- If you truly must build a custom interactive element, ARIA is the
     minimum, not the whole job -- keyboard handling is still on you: -->
<div role="button" tabindex="0"
     onclick="save()"
     onkeydown="if (event.key === 'Enter' || event.key === ' ') save()">
  Save
</div>
<!-- Valid, but strictly more code and more ways to get it wrong
     than just writing <button>Save</button> -->
```

```html
<!-- ARIA covering a genuine gap: no native element expresses "loading status" -->
<div aria-live="polite" id="status"></div>
<script>
  document.getElementById('status').textContent = 'Changes saved.';
  // Screen readers announce this text automatically when it changes,
  // without moving focus -- there's no native HTML element for this.
</script>
```

```html
<!-- aria-label for an icon-only button with no visible text -->
<button aria-label="Close dialog" onclick="closeDialog()">
  <svg aria-hidden="true"><!-- X icon --></svg>
</button>
<!-- aria-hidden="true" on the icon prevents it from being separately
     (and uselessly) announced, since the button's aria-label already covers it -->
```

---

## 🚀 Real World Applications

- E-commerce checkout forms with correctly associated `<label>`s let screen reader users fill out address and payment fields without guessing what each blank is for.
- Single-page apps use `aria-live` regions to announce async results — "Search returned 12 results," a form validation error, a cart update — none of which trigger a page reload for the browser to announce naturally.
- Icon-only toolbar buttons (a trash icon, a hamburger menu) rely on `aria-label` since there's no visible text to form the accessible name.
- Custom widgets that genuinely have no native equivalent — a date-picker calendar grid, a rich autocomplete combobox — lean on the ARIA Authoring Practices patterns as a foundation, precisely because plain HTML has nothing built for them.
- Automated accessibility audits (axe, Lighthouse) flag missing form labels and images without `alt` as some of the highest-impact, most common violations found in the wild.

---

## ⚖️ Advantages

- Native elements + correct labels get you the majority of accessibility for free, with no ARIA at all.
- ARIA fills real, otherwise-unsolvable gaps for dynamic and custom UI.
- Accessible markup tends to also be more robust markup generally — keyboard support and clear structure benefit every user, not only assistive tech users.
- Automated tooling can catch a good fraction of these issues (missing labels, missing alt, bad ARIA) before they ship.

---

## ⚠️ Limitations

- ARIA can't grant behavior — adding a role never gives you keyboard handling, focus management, or state changes automatically; you must implement those yourself.
- Automated accessibility checkers catch structural problems (missing label, missing alt) but can't judge whether `alt` text or `aria-label` wording is actually *good* — that still needs a human.
- Over-applying ARIA "for safety" (redundant roles on elements that already have that role natively, like `role="button"` on an actual `<button>`) adds noise and can occasionally cause screen readers to announce things twice.
- Real accessibility validation ultimately requires testing with actual assistive technology (a real screen reader, real keyboard-only navigation) — no static analysis fully substitutes for that.

---

## 🚨 Common Mistakes

- Building custom clickable `<div>`s or `<span>`s instead of `<button>`, then trying to patch in accessibility with ARIA after the fact rather than starting with the native element.
- Using `placeholder` text as a substitute for a real `<label>` — placeholders disappear on input and are not a reliable accessible name.
- A `<label>` whose `for` attribute doesn't match the input's `id` (often after a refactor), silently breaking the association with no visual sign anything is wrong.
- Adding `role="button"` or `role="link"` without also adding the keyboard behavior (`tabindex`, keydown handling) those roles imply.
- Sprinkling ARIA attributes speculatively because they "seem like they'd help" without understanding what each one actually promises to assistive tech.

---

## 📖 Further Reading

- MDN: "ARIA" and "HTML: A good basis for accessibility"
- W3C WAI-ARIA Authoring Practices Guide (APG)
- The A11y Project — a11yproject.com checklist

---

## 💡 Wisdom from Mímir

The most useful accessibility habit I know is boringly simple: before writing a single ARIA attribute, ask "does a native HTML element already do this?" Nine times out of ten it does, and reaching for it is both less code and more reliable than any amount of careful ARIA. Save ARIA for the genuine remainder — the custom widgets and live regions HTML truly has no vocabulary for — and treat every attribute you add there as a contract you're personally responsible for keeping with real keyboard and focus behavior, not just a label you paste on for good conscience.

---

## 🔗 Related Notes

- [[Semantic HTML and Why It Matters]] — why native elements come with accessibility built in
- [[The label Element and Form Accessibility]] — a deeper look at correct label association and its edge cases
- [[Buttons vs Links - button vs a]] — the most common native-element-vs-div accessibility decision
- [[div and span - Generic Containers and When to Avoid Them]] — the flip side: when a generic container really is correct
