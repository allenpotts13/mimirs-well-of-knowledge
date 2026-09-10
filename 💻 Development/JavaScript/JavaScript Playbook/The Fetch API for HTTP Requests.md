---
type: concept
status: active
created: 2026-09-02
updated: 2026-09-06
technology: JavaScript
difficulty: Intermediate
tags:
  - fetch-api
  - http
  - networking
  - promises
  - web-apis
aliases:
  - fetch()
  - The Fetch API
---

# 📚 The Fetch API for HTTP Requests

> *`fetch()` is the modern, Promise-based way to make HTTP requests from JavaScript — but it only rejects on a network failure, never on an HTTP error status, which trips up nearly everyone the first time.*

---

## 🎯 Purpose

Every web app needs to talk to a server — loading data, submitting a form, checking if a username is taken. The Fetch API exists to make that conversation feel native to modern JavaScript: it returns a Promise, which means it plays nicely with `async`/`await`, `.then()` chains, and everything else in the Promise ecosystem. It replaced the older `XMLHttpRequest` object, which worked but required a verbose, callback-heavy, distinctly clunky API to do the same job.

---

## 🧠 Key Ideas

- `fetch(url)` immediately returns a **Promise that resolves to a `Response` object** — it does not return the data itself, and it does not wait for the body to download before resolving.
- **The critical gotcha:** that Promise resolves successfully even when the server responds with an HTTP error status like `404 Not Found` or `500 Internal Server Error`. `fetch()` only *rejects* on a genuine network-level failure — DNS failure, no connection, CORS block, request aborted. As far as `fetch()` is concerned, "the server responded, just with bad news" still counts as success.
- Because of that, you must manually check `response.ok` (a boolean shorthand for "status is in the 200–299 range") or inspect `response.status` yourself — relying on `.catch()` to catch a 404 will not work, because `.catch()` never fires for it.
- Reading the actual body — `response.json()`, `response.text()`, `response.blob()` — is itself **asynchronous and returns its own separate Promise**, because the body may still be streaming in over the network even after the headers (and therefore the `Response` object) have already arrived.
- `fetch()` doesn't send cookies cross-origin by default, doesn't reject on redirects silently the way some assume, and has no built-in request timeout — all things `XMLHttpRequest` or a library like Axios historically handled differently.
- An `AbortController` is the standard way to cancel an in-flight `fetch()` request, something that required significantly more boilerplate with `XMLHttpRequest`.

---

## ⚙️ How It Works

Think of `fetch()` as sending a letter and getting back a *notification that mail arrived* — not the letter's contents yet. The Promise that `fetch()` returns resolves as soon as the server has responded with *something*, headers included, regardless of whether that something is a 200 "here's your data" or a 404 "couldn't find that." The mail carrier delivered a response; whether that response is good news or bad news is a completely separate question that `fetch()` itself doesn't answer for you.

That's why checking `response.ok` matters: it's you actually opening the envelope and reading whether it says "success" or "error" on it, rather than assuming that because *a* letter arrived, it must contain what you asked for.

Then, actually reading the contents — calling `.json()` — is a second, separate wait, because the body of the response might still be streaming across the network even though the headers already arrived and let the first Promise resolve. That's why `.json()` returns its own Promise that you also need to `await` (or `.then()`).

Compare this to the older `XMLHttpRequest`: it required constructing an object, opening a request, attaching multiple event listener callbacks (`onload`, `onerror`, `onreadystatechange`), and manually checking `xhr.status` inside those callbacks — all before you even started parsing a response. `fetch()` collapses that into a Promise chain, which is a large part of why it displaced `XMLHttpRequest` for new code.

---

## 💻 Examples

```javascript
// The gotcha in action: fetch() resolves even on a 404
async function getUser(id) {
  const response = await fetch(`https://api.example.com/users/${id}`);
  console.log(response.ok);     // false, if the user doesn't exist
  console.log(response.status); // 404
  // fetch() did NOT throw or reject here — it resolved just fine.
  const data = await response.json(); // still works — parses whatever error body the server sent
  return data;
}
```

```javascript
// The correct pattern: manually check response.ok before trusting the data
async function getUserSafely(id) {
  try {
    const response = await fetch(`https://api.example.com/users/${id}`);

    if (!response.ok) {
      // This is the ONLY reliable way to detect an HTTP-level error with fetch.
      throw new Error(`Request failed with status ${response.status}`);
    }

    const data = await response.json(); // a second, separate async step
    return data;
  } catch (error) {
    // This catches genuine network failures (offline, DNS, CORS)
    // AND the Error we manually threw above for a bad status.
    console.error("Could not fetch user:", error.message);
    return null;
  }
}
```

```javascript
// POST request with a JSON body
async function createUser(userData) {
  const response = await fetch("https://api.example.com/users", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(userData),
  });

  if (!response.ok) {
    throw new Error(`Failed to create user: ${response.status}`);
  }

  return response.json();
}
```

```javascript
// Cancelling a request with AbortController — something XMLHttpRequest
// made far more awkward
function fetchWithTimeout(url, timeoutMs = 5000) {
  const controller = new AbortController();
  const timeoutId = setTimeout(() => controller.abort(), timeoutMs);

  return fetch(url, { signal: controller.signal }).finally(() => {
    clearTimeout(timeoutId);
  });
}

try {
  const response = await fetchWithTimeout("https://api.example.com/slow-endpoint", 3000);
  console.log(await response.json());
} catch (error) {
  if (error.name === "AbortError") {
    console.log("Request timed out");
  }
}
```

```javascript
// Old-school comparison: the same request with XMLHttpRequest
function getUserOldSchool(id, onSuccess, onError) {
  const xhr = new XMLHttpRequest();
  xhr.open("GET", `https://api.example.com/users/${id}`);
  xhr.onload = () => {
    if (xhr.status >= 200 && xhr.status < 300) {
      onSuccess(JSON.parse(xhr.responseText));
    } else {
      onError(new Error(`Status ${xhr.status}`));
    }
  };
  xhr.onerror = () => onError(new Error("Network error"));
  xhr.send();
}
// Notice: no Promises, manual status checking, and callbacks instead of
// await — this is exactly the verbosity fetch() was designed to replace.
```

---

## 🚀 Real World Applications

- Loading data from a REST or GraphQL API to populate a page after it has already loaded (an SPA's typical data-fetching pattern).
- Submitting form data to a backend endpoint without a full page reload.
- Polling an endpoint at intervals to check job status (a video processing job, a long-running export).
- Uploading files via `FormData` in the request body.
- Cancelling a stale request when a user types a new search query before the previous request finished (paired with `AbortController`).

---

## ⚖️ Advantages

- Native to the browser (and Node.js since v18) — no library required for basic use.
- Promise-based, so it integrates cleanly with `async`/`await` and Promise combinators like `Promise.all()`.
- A cleaner, more modern API surface than `XMLHttpRequest` — fewer callbacks, more readable chains.
- Built-in streaming support via the `Response` body being a readable stream, useful for large downloads.

---

## ⚠️ Limitations

- Does not reject on HTTP error statuses, which is a genuine design wart that catches out nearly every developer at least once.
- No built-in request timeout — you must implement it yourself with `AbortController` and `setTimeout`.
- No built-in automatic JSON parsing of the request body's `Content-Type`, no built-in retry logic, and no built-in interceptors — libraries like Axios or `ky` add these conveniences on top.
- Error messages on network failure (`TypeError: Failed to fetch`) are famously unhelpful about *why* the request failed — CORS, offline, DNS, and a malformed URL can all produce the same vague error.

---

## 🚨 Common Mistakes

- Relying on `.catch()` alone to handle a 404 or 500 — it will never fire for those, because `fetch()` doesn't reject on HTTP error statuses.
- Forgetting that `response.json()` is itself async and needs its own `await` or `.then()` — trying to use the parsed data synchronously right after calling `.json()`.
- Calling `.json()` on a response body that isn't actually JSON (an HTML error page from a misconfigured server, for instance), which throws a confusing `SyntaxError` far from the real problem.
- Not checking `response.ok` before parsing the body, then being surprised when "successful" code runs against an error payload.
- Assuming `fetch()` automatically sends cookies or handles CORS the way a same-origin form submission would — cross-origin requests need explicit `credentials: "include"` and proper CORS headers on the server.

---

## 📖 Further Reading

- MDN: "Using the Fetch API" and "Response"
- MDN: "AbortController"
- web.dev: "Making network requests" guides on fetch and error handling

---

## 💡 Wisdom from Mímir

Every developer eventually gets burned by shipping code where a `.catch()` block is supposed to handle "the API said no" and it just never runs — because `fetch()` doesn't consider a 404 to be a failure, only *you* do. Write `if (!response.ok) throw ...` as a reflex, every single time, the same way you'd reflexively check for `null` before calling a method on something. It costs you one line and saves you an afternoon of "why isn't my error handling working."

---

## 🔗 Related Notes

- [[Promises]]
- [[Async-Await]]
- [[Error Handling with try-catch]]
- [[JSON - Parsing and Stringifying]]
