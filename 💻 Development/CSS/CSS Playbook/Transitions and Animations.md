---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: CSS
difficulty: Intermediate
tags:
  - transitions
  - animations
  - keyframes
  - performance
aliases:
  - CSS Transitions
  - CSS Animations
  - "@keyframes"
publish: true
---

# <span class="rune">ᛟ</span> Transitions and Animations

> *`transition` smoothly animates a property change that's already triggered by something else (like `:hover`), while `@keyframes` + `animation` define a self-running, multi-step animation with no trigger needed at all.*

---

## 🎯 Purpose

An instant jump from one style to another — a menu that just *appears*, a button that *snaps* to a new color — reads as jarring and cheap. Smoothly interpolating between states makes an interface feel responsive and intentional instead. CSS gives you two distinct tools for this because they solve two different problems: reacting smoothly to a state change someone else causes, versus running a defined sequence of your own that doesn't need any external trigger at all.

---

## 🧠 Key Ideas

- **`transition`** smoothly animates a property **between two values**, but only in response to that value actually changing — typically triggered by a state change like `:hover`, `:focus`, or a class toggled via JavaScript.
- **`@keyframes`** defines a **named sequence of steps** (`0%`, `50%`, `100%`, etc.), and **`animation`** applies that sequence to an element — it's **self-running**, needs no external trigger, and can loop indefinitely (`animation-iteration-count: infinite`).
- `transition` only ever knows about a **start** and an **end** state (plus an optional easing curve between them) — it has no concept of intermediate steps the way `@keyframes` does.
- `animation` gives far more control: looping, direction reversal (`alternate`), pausing, delays, and multiple distinct keyframe steps in a single animation.
- **Performance matters enormously here**: animating `transform` and `opacity` is cheap — the browser can composite them on the GPU without recalculating layout. Animating `width`, `height`, `top`, or `left` is expensive — each frame forces a full layout recalculation ("reflow"), which is where janky, stuttering animations come from.
- Both `transition` and `animation` respect `prefers-reduced-motion` best practices when authors explicitly account for it — neither does so automatically.

---

## ⚙️ How It Works

`transition` is reactive — think of it as telling the browser, "whenever this property's value changes, for whatever reason, don't jump straight there — ease into it smoothly over this duration." It doesn't cause anything to change itself; it just decorates a change that's already going to happen, most commonly a pseudo-class flipping (`:hover`) or a class being added/removed by JavaScript. If nothing ever changes the property, the transition never fires — it has nothing to interpolate.

`@keyframes` + `animation` is proactive — it's a self-contained performance with its own script, running the moment it's applied, independent of any hover or click. You define named checkpoints (`0%` through `100%`, or `from`/`to`) describing what the element should look like at each percentage of the animation's duration, and the browser interpolates smoothly between each pair of checkpoints in sequence. This is what lets you build a loading spinner that spins forever, a pulsing notification badge, or a multi-step entrance animation that moves, fades, *and* scales in three distinct phases — none of which `transition` alone could express, since `transition` only ever has two endpoints to work with.

The performance distinction between "cheap" and "expensive" properties comes down to what stage of the rendering pipeline a property change forces the browser to redo. `transform` and `opacity` can be handled almost entirely by the compositor (often GPU-backed) — the browser just repaints an existing layer in a new position, size, or transparency, without asking "does anything else on the page need to move because of this?" Properties like `width`, `height`, `top`, or `left` do force that question — the browser has to recompute the geometry of the changed element *and potentially everything affected around it*, every single frame of the animation. At 60fps that's a lot of expensive recalculation for something that could often have been expressed as a `transform` instead.

---

## 💻 Examples

```css
/* transition: smoothly animates a property change triggered by :hover */
.button {
  background: #3498db;
  transition: background 0.2s ease, transform 0.2s ease;
}

.button:hover {
  background: #2980b9;
  transform: translateY(-2px);
}
```

```css
/* transition triggered by a JS-toggled class instead of a pseudo-class */
.menu {
  opacity: 0;
  transform: translateY(-10px);
  transition: opacity 0.3s ease, transform 0.3s ease;
}

.menu.is-open {
  opacity: 1;
  transform: translateY(0);
}
```

```css
/* @keyframes + animation: self-running, no trigger needed, can loop */
@keyframes pulse {
  0%   { transform: scale(1);   opacity: 1; }
  50%  { transform: scale(1.15); opacity: 0.7; }
  100% { transform: scale(1);   opacity: 1; }
}

.notification-badge {
  animation: pulse 1.5s ease-in-out infinite;
}
```

```css
/* A multi-step entrance animation — impossible to express with transition alone,
   since transition only ever has a start and an end, not three distinct phases */
@keyframes slide-fade-in {
  0%   { opacity: 0; transform: translateY(20px); }
  60%  { opacity: 1; transform: translateY(-4px); } /* slight overshoot */
  100% { opacity: 1; transform: translateY(0); }
}

.card {
  animation: slide-fade-in 0.5s ease-out;
}
```

```css
/* PERFORMANT: animating transform/opacity — GPU-composited, no layout recalculation */
.good {
  transition: transform 0.3s ease;
}
.good:hover {
  transform: scale(1.05);
}

/* EXPENSIVE: animating width/top forces layout recalculation every frame */
.avoid {
  width: 100px;
  top: 0;
  transition: width 0.3s ease, top 0.3s ease; /* triggers reflow every frame */
}
.avoid:hover {
  width: 150px; /* prefer transform: scale() instead */
  top: 10px;    /* prefer transform: translateY() instead */
}
```

---

## 🚀 Real World Applications

- Button, link, and card hover/focus states — color shifts, subtle lifts, and scale changes via `transition`.
- Modal and dropdown open/close animations, triggered by a JS-toggled class and handled entirely in CSS via `transition`.
- Loading spinners, skeleton screens, and pulsing "new content" indicators built with looping `@keyframes` animations.
- Page-load entrance animations (fade-and-slide-in cards, staggered list items) using `animation` with a `animation-delay` per item.
- Attention-directing micro-interactions — a "shake" animation on an invalid form field, a brief "bounce" on a newly added cart item.

---

## ⚖️ Advantages

- Both are pure CSS — no JavaScript animation library or `requestAnimationFrame` loop needed for the vast majority of UI motion.
- `transition` requires minimal code for simple, common cases (hover/focus feedback) and reads clearly in the stylesheet next to the property it affects.
- `animation` + `@keyframes` unlocks looping, multi-step, and reversible sequences transition alone can't express.
- Animating `transform`/`opacity` specifically is fast enough to stay smooth even on lower-powered devices, since it avoids layout and often runs on the GPU.

---

## ⚠️ Limitations

- `transition` can't run on its own — it needs an actual value change to react to, so it's useless for anything that should play automatically without user interaction or a class toggle.
- `transition` has no concept of intermediate keyframes — only a start and end value, interpolated by the easing function.
- Complex `animation` sequences can get hard to read and maintain directly in CSS as the number of keyframe steps and properties grows.
- Not every property is meaningfully animatable, and not every animatable property is *cheap* to animate — `width`/`height`/`top`/`left` will work but cost real performance, especially on lower-end devices or with many animated elements at once.
- Neither `transition` nor `animation` respects a user's reduced-motion preference automatically — that requires explicitly wrapping the animation in an `@media (prefers-reduced-motion: reduce)` query.

---

## 🚨 Common Mistakes

- Animating `width`/`height`/`top`/`left` for something that could have been expressed as `transform: scale()`/`translate()`, causing avoidable layout thrashing and janky motion.
- Reaching for `@keyframes` when a simple `transition` on `:hover` would do — adding unnecessary complexity for a two-state change.
- Forgetting that `transition` only fires on an actual value change — setting `transition: all 0.3s;` on an element and expecting it to animate in on page load with no state change to trigger it.
- Using `transition: all` broadly instead of naming specific properties — it works, but it's harder to reason about performance and can accidentally animate properties you didn't intend to.
- Not accounting for `prefers-reduced-motion`, leaving motion-sensitive users with animations they explicitly asked their OS to minimize.

---

## 📖 Further Reading

- MDN: "Using CSS transitions"
- MDN: "Using CSS animations"
- web.dev: "Animations and performance"

---

## 💡 Wisdom from Mímir

The question I ask myself before writing any animation is simple: "does this need to run on its own, or is it just reacting to something?" That single question picks the right tool almost every time — reacting to hover/focus/a toggled class is `transition`'s job; anything self-running, looping, or with more than two visual states is `@keyframes`. And regardless of which one I reach for, I check what properties I'm actually animating before shipping it — if I catch myself animating `width` or `top`, I stop and ask whether a `transform` equivalent gets the same visual result, because it almost always does, for a fraction of the cost.

---

## 🔗 Related Notes

- [[The transform Property]] — the property most commonly paired with both transition and animation for performant motion
- [[z-index and Stacking Contexts]] — animating opacity/transform creates a stacking context, worth knowing before layering animated elements
- [[Pseudo-Classes and Pseudo-Elements]] — `:hover` and `:focus` are the most common transition triggers
- [[Positioning - static, relative, absolute, fixed, and sticky]] — animating top/left requires positioning context and is the expensive path this note warns against
