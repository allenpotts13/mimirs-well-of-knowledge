---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-06
technology: JavaScript
difficulty: Beginner
tags:
  - web-storage
  - localstorage
  - sessionstorage
  - browser-apis
  - security
aliases:
  - Web Storage API
  - localStorage vs sessionStorage
publish: true
---

# <span class="rune">ᛟ</span> Local Storage vs Session Storage

> *Both are simple, synchronous, string-only key-value stores built into the browser — the only real difference between them is how long the data sticks around.*

---

## 🎯 Purpose

Sometimes a web page needs to remember something between page loads without hitting a server at all — a user's theme preference, a draft of a form they're filling out, an auth token, a "don't show this again" flag. `localStorage` and `sessionStorage` exist to give JavaScript a small, simple, built-in place to stash that kind of data directly in the browser, with no backend, no database, and no cookies-style overhead attached to every single HTTP request.

---

## 🧠 Key Ideas

- **`localStorage`** persists indefinitely — the data survives closing the tab, closing the browser entirely, and even restarting the computer. It only goes away when explicitly cleared (by code, by the user clearing site data, or by the browser under storage pressure).
- **`sessionStorage`** is scoped to a single tab's lifetime — it clears automatically the moment that tab or window is closed. Duplicate a tab, and the copy gets its own separate `sessionStorage`, not a shared one.
- Both share the identical API — `.setItem(key, value)`, `.getItem(key)`, `.removeItem(key)`, `.clear()` — and both are scoped per **origin** (protocol + domain + port), so `https://example.com` and `http://example.com` (different protocol) do not share storage.
- Both are **synchronous** and **string-only**. Trying to store an object directly stores the literal string `"[object Object]"` — you must `JSON.stringify()` it going in and `JSON.parse()` it coming back out.
- Both have a practical size ceiling of roughly **5–10MB per origin**, depending on the browser — plenty for preferences and small caches, nowhere near enough for large datasets or files.
- **Neither is safe for sensitive data.** Any JavaScript running on the page — including a malicious script injected via an XSS vulnerability — can read both in full. Unlike cookies, there is no `httpOnly` equivalent that hides the data from script access.

---

## ⚙️ How It Works

Think of `localStorage` as a filing cabinet bolted to the floor of the building (the browser, for that specific website) — it stays exactly where it is, holding exactly what you put in it, until someone deliberately empties a drawer. Close the office for the night, come back next week, the cabinet is untouched.

`sessionStorage` is more like a whiteboard in a single meeting room, tied to that one meeting (the tab). The moment the meeting ends and everyone leaves the room (the tab closes), the whiteboard gets wiped clean. Open a second meeting room to discuss the same topic (duplicate the tab), and it gets its own blank whiteboard — it doesn't inherit what was written on the first one, because it's a genuinely separate session, not a copy of the state.

Both storages only ever hold strings — imagine every drawer in the cabinet and every corner of the whiteboard only accepts index cards, never actual physical objects. If you want to store something more complex, like a user preferences object, you have to write it out as a formatted string first (`JSON.stringify`) and read that formatting back off the card to reconstruct the object later (`JSON.parse`).

And because both are just JavaScript-readable storage sitting in the page's own origin, with no special protection, anyone who manages to run *their* JavaScript on your page — through an XSS hole in a comment field you didn't sanitize properly, say — can open that same filing cabinet and read everything in it, including anything that looks like an auth token or session identifier.

---

## 💻 Examples

```javascript
// Basic localStorage usage — persists across browser restarts
localStorage.setItem("theme", "dark");
console.log(localStorage.getItem("theme")); // "dark"

localStorage.removeItem("theme");
console.log(localStorage.getItem("theme")); // null

// Basic sessionStorage usage — cleared when the tab closes
sessionStorage.setItem("draftMessage", "Hey, are you free at 3pm?");
console.log(sessionStorage.getItem("draftMessage")); // "Hey, are you free at 3pm?"
// Close this tab, open a new one to the same site: draftMessage is gone.
```

```javascript
// Storing objects: must stringify going in, parse coming back out
const preferences = { theme: "dark", fontSize: 16, notifications: true };

localStorage.setItem("preferences", JSON.stringify(preferences));

const stored = localStorage.getItem("preferences");
console.log(stored); // '{"theme":"dark","fontSize":16,"notifications":true}' — just a string!

const parsedPreferences = JSON.parse(stored);
console.log(parsedPreferences.fontSize); // 16 — now it's a real object again

// A common mistake, shown for contrast:
localStorage.setItem("oops", preferences); // no JSON.stringify
console.log(localStorage.getItem("oops")); // "[object Object]" — data is lost
```

```javascript
// A small helper to avoid repeating the JSON dance everywhere
const storage = {
  set(key, value) {
    localStorage.setItem(key, JSON.stringify(value));
  },
  get(key, fallback = null) {
    const raw = localStorage.getItem(key);
    if (raw === null) return fallback;
    try {
      return JSON.parse(raw);
    } catch {
      return fallback; // handles corrupted or unexpectedly non-JSON data
    }
  },
};

storage.set("cart", [{ id: 1, qty: 2 }]);
console.log(storage.get("cart")); // [{ id: 1, qty: 2 }]
console.log(storage.get("missingKey", [])); // [] — the fallback
```

```javascript
// Listening for changes made in OTHER tabs (localStorage only)
window.addEventListener("storage", (event) => {
  console.log(`"${event.key}" changed from`, event.oldValue, "to", event.newValue);
});
// This fires in every OTHER tab of the same origin when one tab calls
// localStorage.setItem() — useful for syncing state like "user logged out"
// across multiple open tabs. It does NOT fire in the tab that made the change.
```

```javascript
// What NOT to do: storing a real auth/session token
localStorage.setItem("authToken", "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."); 
// If this site has even one XSS vulnerability anywhere, an attacker's
// injected script can run `localStorage.getItem("authToken")` and steal
// this token just as easily as your own code can read it. An httpOnly
// cookie, which JavaScript cannot read at all, is the safer place for this.
```

---

## 🚀 Real World Applications

- Remembering a user's UI preference (dark mode, sidebar collapsed state, chosen language) across visits with `localStorage`.
- Autosaving an in-progress form or draft so a refresh doesn't lose the user's work, using `sessionStorage` if it should only survive the current visit.
- Caching non-sensitive API responses to reduce redundant network calls (a "last fetched at" timestamp plus the data).
- Tracking a multi-step wizard or checkout flow's current step within a single browsing session (`sessionStorage`), so a duplicated tab starts its own fresh flow.
- A "dismiss this banner" flag so a promotional or cookie-consent banner doesn't reappear every single page load.

---

## ⚖️ Advantages

- Extremely simple, synchronous API — no async ceremony needed for basic reads and writes.
- No server round-trip required, unlike storing the same preference in a database tied to a user account.
- Larger capacity than cookies (which are typically capped around 4KB and sent with every matching HTTP request, adding overhead).
- Scoped automatically per origin, so different sites' data can never collide or leak into each other.

---

## ⚠️ Limitations

- Synchronous access means large reads/writes can block the main thread, unlike `IndexedDB`, which is asynchronous and better suited to larger amounts of structured data.
- String-only storage means every non-string value needs manual serialization, and a malformed stored string can throw when parsed back.
- Not available in some contexts (private/incognito windows in some browsers historically capped or disabled it; certain browser settings can disable it outright), so code that assumes it's always present can break.
- Neither is appropriate for anything sensitive, since any script on the page — trusted or injected — can read it in full.
- Storage is per-origin and per-browser — it doesn't sync across a user's different devices or browsers the way server-stored account data would.

---

## 🚨 Common Mistakes

- Storing an object or array directly without `JSON.stringify()` first, then being confused by a stored value of the literal string `"[object Object]"`.
- Forgetting to wrap `JSON.parse()` in a `try`/`catch` — a corrupted or manually-edited stored value will throw a `SyntaxError` at read time.
- Storing sensitive data like auth tokens, passwords, or personal information in either storage, assuming it's private because "only my code accesses it" — it's exposed to any script on the page, including ones from a successful XSS attack.
- Assuming `sessionStorage` is shared across tabs of the same site — it's actually isolated per tab, which surprises people expecting it to behave like a shared session.
- Not handling the case where storage is full or disabled (`setItem` can throw a `QuotaExceededError`), leaving the app in a broken state on an otherwise minor failure.

---

## 📖 Further Reading

- MDN: "Window: localStorage property" and "Window: sessionStorage property"
- MDN: "Using the Web Storage API"
- OWASP: guidance on client-side storage and XSS risk (search "OWASP DOM-based XSS" and "OWASP HTML5 Security Cheat Sheet")

---

## 💡 Wisdom from Mímir

The line I repeat to anyone reaching for `localStorage` to hold a token is this: if the data would be genuinely bad news for a stranger to read off your user's browser, it doesn't belong in Web Storage — full stop, no matter how convenient it is to grab with `.getItem()`. Convenience and safety pull in opposite directions here, and it's an easy trade to get wrong because the code *works* right up until the day someone finds the XSS hole that turns "works" into "leaked."

---

## 🔗 Related Notes

- [[JSON - Parsing and Stringifying]]
- [[DOM Manipulation Basics]]
- [[The Fetch API for HTTP Requests]]
