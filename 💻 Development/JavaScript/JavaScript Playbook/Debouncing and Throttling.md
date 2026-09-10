---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-06
technology: JavaScript
difficulty: Intermediate
tags:
  - performance
  - debounce
  - throttle
  - closures
  - event-handling
aliases:
  - Debounce vs Throttle
  - Rate Limiting Function Calls
publish: true
---

# 📚 Debouncing and Throttling

> *Debouncing waits for a burst of activity to go quiet before acting once; throttling lets activity through, but only ever at a fixed maximum rate — two different answers to the same problem of a function being called far too often.*

---

## 🎯 Purpose

Some events fire at an alarming rate — typing in a search box can fire a `keyup` dozens of times a second, scrolling and resizing can fire hundreds of times as the user drags. If your handler does something expensive on every single one of those events — an API call, a layout recalculation, a heavy re-render — you'll bury the browser (or your server) under far more work than the situation actually calls for. Debouncing and throttling exist to tame that firehose, each in a different way suited to a different kind of situation.

---

## 🧠 Key Ideas

- **Debounce** delays running a function until a burst of calls has gone quiet for a specified period — every new call *resets the clock*, so the function only actually runs once the calls stop coming, not while they're still arriving.
- The classic debounce use case is **search-as-you-type**: you don't want to hit the API on every keystroke, only once the user has actually paused, meaning they've likely finished typing what they meant to search for.
- **Throttle** guarantees a function runs at most once per fixed interval, no matter how many times it's actually called during that interval — it doesn't wait for quiet, it just enforces a speed limit.
- The classic throttle use case is a **scroll or resize handler**: you still want updates *during* continuous scrolling, just not on every single pixel of movement — once every 100–200ms is plenty to feel responsive without being wasteful.
- Both are implemented the same underlying way: a **closure** wraps the original function and holds onto some piece of state between calls — a timer ID for debounce, or a last-called timestamp (and sometimes also a timer ID) for throttle — which is exactly what makes the wrapped version "remember" what happened on the previous call.
- Neither changes *what* the function does — both just control *when* and *how often* it's allowed to run.

---

## ⚙️ How It Works

**Debounce** works like an elevator that waits for people to stop pressing the "door open" button before it finally starts moving. Every time someone presses it again, the wait timer resets back to full — the elevator doesn't leave until there's been a genuine pause with nobody pressing anything. Apply that to a search box: every keystroke says "wait, don't search yet, more might be coming" and resets a timer. Only once the user stops typing for the full quiet period does the timer finally run out uninterrupted, and *that's* when the actual search fires.

**Throttle** works more like a bouncer at a door who only lets one person through every 30 seconds, no matter how many people are crowding to get in. It doesn't care about "quiet periods" at all — people can be arriving constantly — it simply enforces "at most one admission per interval," rejecting (or queuing, depending on implementation) everyone else who shows up in between. Apply that to a scroll handler: the user might be scrolling continuously for ten seconds straight, generating hundreds of events, but the throttled handler only actually executes on a steady drip — once every interval — regardless of how densely packed the real events are.

Both rely on a closure to remember state between calls, because a plain function has no memory of what happened the last time it ran — each call is otherwise a blank slate. Wrapping the function in an outer function that holds a `timeoutId` or `lastRan` variable in its closure gives the wrapped version exactly the memory it needs: debounce needs to remember "is there already a pending timer I should cancel and restart," and throttle needs to remember "how long has it actually been since I last let this through."

---

## 💻 Examples

```javascript
// Debounce implementation — delays until calls have stopped for `delay` ms
function debounce(fn, delay) {
  let timeoutId; // held in the closure, remembered between calls

  return function (...args) {
    clearTimeout(timeoutId); // cancel any pending run — new activity resets the clock
    timeoutId = setTimeout(() => {
      fn.apply(this, args);
    }, delay);
  };
}

// Usage: search-as-you-type
function searchAPI(query) {
  console.log(`Searching for: "${query}"`);
}
const debouncedSearch = debounce(searchAPI, 300);

// Simulating fast typing — only the LAST call actually fires, 300ms after typing stops
debouncedSearch("j");
debouncedSearch("ja");
debouncedSearch("jav");
debouncedSearch("java");
debouncedSearch("javascript");
// ...300ms of silence later:
// Searching for: "javascript"   <- only this one ever runs
```

```javascript
// Throttle implementation — runs at most once per `interval` ms
function throttle(fn, interval) {
  let lastRan = 0; // timestamp of the last actual execution, held in the closure

  return function (...args) {
    const now = Date.now();
    if (now - lastRan >= interval) {
      fn.apply(this, args);
      lastRan = now;
    }
    // calls that arrive before the interval has elapsed are simply dropped
  };
}

// Usage: a scroll handler that only updates a "scroll progress" indicator
// once every 200ms, no matter how fast the user scrolls
function updateScrollIndicator() {
  console.log(`Scroll position: ${window.scrollY}px`);
}
const throttledScroll = throttle(updateScrollIndicator, 200);
window.addEventListener("scroll", throttledScroll);
// Even if "scroll" fires 60 times a second while scrolling, updateScrollIndicator
// only actually runs about 5 times a second (once every 200ms).
```

```javascript
// A throttle variant that also guarantees a final "trailing" run,
// so the very last event during a burst isn't silently dropped
function throttleWithTrailing(fn, interval) {
  let lastRan = 0;
  let trailingTimeoutId = null;

  return function (...args) {
    const now = Date.now();
    const remaining = interval - (now - lastRan);

    if (remaining <= 0) {
      clearTimeout(trailingTimeoutId);
      fn.apply(this, args);
      lastRan = now;
    } else {
      // schedule one trailing call so the final event in a burst still lands
      clearTimeout(trailingTimeoutId);
      trailingTimeoutId = setTimeout(() => {
        fn.apply(this, args);
        lastRan = Date.now();
      }, remaining);
    }
  };
}
```

```javascript
// Side-by-side mental model with a fast burst of calls
// Imagine calls arriving at t=0, 50, 100, 150, 200, 600 (ms), with a 300ms setting

// debounce(fn, 300): only fires ONCE, at t=500 (300ms after the LAST call in the
// burst at t=200), then again at t=900 for the isolated call at t=600.

// throttle(fn, 300): fires at t=0 (first call, immediately), then again around
// t=300 if a call arrives after that point, and again for the call at t=600.
```

---

## 🚀 Real World Applications

- Search-as-you-type boxes and autocomplete inputs, debounced so the API is only hit once the user pauses.
- Window `resize` handlers that recalculate an expensive layout, throttled so recalculation happens a handful of times per second instead of on every single pixel of resizing.
- Infinite-scroll pagination, throttled on the `scroll` event to check "are we near the bottom?" without checking on every micro-scroll.
- Form validation that runs as the user types, debounced so validation (and any related error messages) doesn't flicker on every keystroke.
- Button click handlers on a "submit" button, debounced (or more precisely, only allowed once) to prevent a slow double-click from firing two form submissions.

---

## ⚖️ Advantages

- Dramatically cuts down the number of expensive operations (API calls, DOM reflows, re-renders) triggered by high-frequency events.
- Both are small, self-contained, dependency-free utility functions — easy to drop into any codebase without a library.
- Improves perceived performance and reduces jank on scroll/resize-heavy interfaces.
- Reduces unnecessary server load from over-eager API calls, which matters at scale.

---

## ⚠️ Limitations

- Debounce introduces a deliberate delay — pick too long a delay and the interface starts to feel sluggish or unresponsive.
- Basic throttle implementations can drop the very last event in a burst (the "trailing" problem), which sometimes means the UI ends up slightly out of sync with the true final state until a trailing-edge variant is used.
- Neither is a substitute for actually optimizing the expensive operation itself — they reduce *how often* it runs, not how *expensive* each run is.
- Choosing the wrong tool for the situation (throttling a search box, or debouncing a scroll-position indicator) produces a noticeably worse experience than picking correctly.

---

## 🚨 Common Mistakes

- Mixing up debounce and throttle — using debounce on a scroll handler means it might never fire at all during continuous scrolling, since the "quiet period" it's waiting for never actually arrives.
- Creating a new debounced/throttled function on every render in a UI framework (e.g., inside a React component body without memoizing it), which means the closure holding the timer/timestamp gets thrown away and recreated constantly, defeating the whole point.
- Picking a delay/interval by guesswork rather than testing — too short barely helps, too long feels broken.
- Forgetting that a debounced function's callback runs later, asynchronously, so anything depending on the current state at call time (rather than at "call resolves" time) may be reading stale values by the time it actually executes.
- Not cleaning up a pending debounce timer when a component unmounts or a page navigates away, leading to a callback firing against elements or state that no longer exist.

---

## 📖 Further Reading

- MDN: "Debouncing and Throttling" concepts are covered practically within lodash's documentation for `_.debounce` and `_.throttle` (a widely used, battle-tested reference implementation of both)
- CSS-Tricks: "Debouncing and Throttling Explained Through Examples"
- MDN: "setTimeout" and "clearTimeout" — the primitives both techniques are built on

---

## 💡 Wisdom from Mímir

The way I keep the two straight, permanently: debounce is for "tell me when they're *done*," throttle is for "keep me updated, but don't drown me." A search box wants to know when typing is done. A scroll handler wants a steady drip of updates the whole time, not silence until the scrolling stops. Get that distinction right and you'll never reach for the wrong one again.

---

## 🔗 Related Notes

- [[Closures and Scope]]
- [[Higher-Order Functions]]
- [[The Event Loop - Call Stack, Task Queue, and Microtasks]]
- [[Event Handling and Event Delegation]]
