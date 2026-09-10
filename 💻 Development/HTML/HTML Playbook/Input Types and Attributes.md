---
type: concept
status: active
created: 2026-09-02
updated: 2026-09-02
technology: HTML
difficulty: Beginner
tags:
  - forms
  - input
  - accessibility
  - fundamentals
aliases:
  - input type attribute
  - HTML Input Types
---

# 📚 Input Types and Attributes

> *The `type` attribute on `<input>` isn't just a styling hint — it changes the mobile keyboard shown, unlocks built-in validation, and tells assistive technology what kind of data it's looking at, all from a single word.*

---

## 🎯 Purpose

Every `<input>` defaults to `type="text"` if you don't specify one, and text will technically *accept* an email address, a phone number, or a date — but accepting isn't the same as understanding. Choosing the correct, specific `type` for the data you're actually collecting is what triggers the browser's specialized behavior for that data: the right on-screen keyboard on mobile, built-in format checking, and native UI (like a date picker) instead of you building one from scratch. This concept exists because "just use `type="text"` for everything" is the single most common way HTML forms leave free functionality on the table.

---

## 🧠 Key Ideas

- `type="email"` triggers an email-optimized mobile keyboard (with `@` and `.com` shortcuts) and free format validation — no `pattern` regex required.
- `type="number"` shows a numeric keypad on mobile and restricts input to numbers, with `min`/`max`/`step` for range and increment control.
- `type="date"`, `type="time"`, and `type="datetime-local"` render a native date/time picker UI, sparing you a JavaScript date-picker library for basic cases.
- `type="tel"` triggers a phone-style numeric keypad on mobile — note it does *not* validate phone number format on its own, since formats vary too much internationally, so pair it with `pattern` if you need format enforcement.
- Defaulting to `type="text"` for everything loses both benefits at once: the wrong keyboard shows up on mobile, and you get zero built-in validation.
- `placeholder` is not a substitute for a `<label>` — it's example/hint text that **disappears the moment the user types**, and is not reliably announced or retained by all assistive technology the way a real label is.

---

## ⚙️ How It Works

The `type` attribute tells the browser what *kind* of data an input holds, and the browser adapts three things based on that: the on-screen keyboard on touch devices, the native validation behavior, and (for some types) the entire input widget itself. `type="email"` swaps in a keyboard with `@` front and center; `type="tel"` swaps in a numeric phone keypad; `type="date"` replaces the plain text box with a calendar picker entirely. None of this requires JavaScript — it's the browser reading one attribute and doing the right thing.

`placeholder` is a different, much narrower tool: greyed-out example text shown *inside* an empty field, meant to hint at expected format (like `placeholder="you@example.com"`). The moment a user types a single character, that hint vanishes — so if it was carrying the field's actual identity (like "Email" instead of a label), the user loses that context entirely once they start typing, and has to rely on memory. A `<label>`, by contrast, stays visible permanently, is reliably announced by screen readers when the field receives focus, and — critically — is clickable/tappable, expanding the field's effective click target. Placeholder text and label text solve different problems: a label says *what this field is*; a placeholder (optionally) hints at *what format to type it in*. Neither should be doing the other's job.

---

## 💻 Examples

```html
<!-- Wrong: type="text" for everything, no format hints, mobile keyboard is generic -->
<label for="email">Email</label>
<input type="text" id="email" name="email">

<label for="phone">Phone</label>
<input type="text" id="phone" name="phone">

<label for="birthday">Birthday</label>
<input type="text" id="birthday" name="birthday">
```

```html
<!-- Right: specific types unlock the correct keyboard and built-in behavior -->
<label for="email">Email</label>
<input type="email" id="email" name="email">
<!-- Mobile keyboard shows @ and .com shortcuts; basic format validated automatically -->

<label for="phone">Phone</label>
<input type="tel" id="phone" name="phone" pattern="[0-9\-\+\s()]+">
<!-- Mobile shows a numeric phone keypad; pattern adds format enforcement since tel alone doesn't -->

<label for="birthday">Birthday</label>
<input type="date" id="birthday" name="birthday">
<!-- Native calendar picker UI, no JS date-picker library needed -->

<label for="quantity">Quantity</label>
<input type="number" id="quantity" name="quantity" min="1" max="10" step="1">
<!-- Numeric keypad on mobile; browser enforces the 1-10 range -->
```

```html
<!-- placeholder vs label: not interchangeable -->

<!-- WRONG: placeholder used as the only identifier for the field -->
<input type="text" name="email" placeholder="Email">
<!-- Once the user types, "Email" disappears — they must remember what field this was.
     Also: many screen readers don't announce placeholder the same reliable way a label is announced. -->

<!-- RIGHT: real label for identity, placeholder (optionally) for format hint -->
<label for="email">Email</label>
<input type="email" id="email" name="email" placeholder="you@example.com">
<!-- "Email" (the label) never disappears; the placeholder just hints at expected format -->
```

---

## 🚀 Real World Applications

- Checkout forms use `type="tel"` for phone numbers and `type="email"` for email specifically so mobile users get the fastest, most relevant keyboard during a moment where friction directly costs conversions.
- Booking and reservation forms use `type="date"`/`type="time"` to get a consistent native picker across platforms without a third-party JS date-picker dependency for simple cases.
- Quantity selectors on e-commerce product pages use `type="number"` with `min`/`max`/`step` to keep users from accidentally ordering a negative or absurd quantity.
- Search bars commonly use `type="search"`, which on some platforms adds a native "clear" (x) button and can trigger a "search"-labeled key on the mobile keyboard instead of a generic "go."
- Accessibility audits specifically flag forms that rely on `placeholder` alone with no associated `<label>`, since it fails WCAG's requirement that form inputs have a persistent, programmatically-associated name.

---

## ⚖️ Advantages

- Specific input types give you a better mobile keyboard, native validation, and sometimes an entire native UI widget, all for free with a single attribute.
- Reduces the amount of custom JavaScript needed for common patterns like date pickers and numeric steppers.
- Improves usability measurably on mobile, where keyboard friction has an outsized effect on form completion rates.
- Using real `<label>` elements alongside the right input type keeps forms both usable and accessible without extra effort.

---

## ⚠️ Limitations

- Native date/time pickers vary in appearance and behavior across browsers, which can be a problem for teams that need pixel-perfect, cross-browser-consistent design.
- `type="tel"` provides no built-in format validation of its own — international phone formats vary too widely for the browser to enforce a single pattern by default.
- Some specialized input types (`type="color"`, `type="range"`) have inconsistent visual styling across browsers and limited styling control.
- Older browsers may fall back to treating an unrecognized `type` value as plain text, so very old target environments need testing.

---

## 🚨 Common Mistakes

- Defaulting to `type="text"` out of habit for emails, numbers, phone numbers, and dates, losing both the mobile keyboard benefit and the built-in validation.
- Using `placeholder` as the only label for a field, leaving the field unlabeled once the user starts typing and for assistive technology.
- Assuming `type="tel"` validates phone number format the way `type="email"` validates email format — it doesn't, by design.
- Forgetting `min`/`max`/`step` on `type="number"` fields, allowing nonsensical values (negative quantities, out-of-range ages) that then have to be caught later.
- Using low-contrast placeholder text as if it were reliably visible design content, when its whole purpose is to be a faint, temporary hint.

---

## 📖 Further Reading

- MDN: "`<input>`: The Input (Form Input) element" (full list of input types)
- MDN: "Placeholder" attribute reference
- web.dev: "Sign-up form best practices" (input type and autofill guidance)

---

## 💡 Wisdom from Mímir

I've started treating `type="text"` as a choice I have to justify, not a default I can reach for without thinking. Every time I catch myself typing `type="text"` for something that clearly has a shape — a date, a number, an email — I stop and ask what specific type already does that job better than I could by hand. Nine times out of ten, the browser's already built exactly what I was about to write myself.

---

## 🔗 Related Notes

- [[Forms and Form Validation]] — how input types combine with validation attributes for a complete, JavaScript-free validation story
- [[The label Element and Form Accessibility]] — why a real `<label>` is never optional, no matter how good your placeholder text is
- [[Accessibility Basics - alt Text, Labels, and ARIA]] — the broader accessibility principles that make labeled, well-typed inputs usable for everyone
- [[Semantic HTML and Why It Matters]] — the general principle that the right element (or attribute) does work for you that a generic one won't
