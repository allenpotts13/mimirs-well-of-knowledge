---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: HTML
difficulty: Beginner
tags:
  - forms
  - http
  - security
  - fundamentals
aliases:
  - GET vs POST
  - form method attribute
publish: true
permalink: html/form-submission-get-vs-post
---

# <span class="rune">ᛟ</span> Form Submission: GET vs POST

> *`method="get"` puts your form data in the URL for the world (and the browser history) to see; `method="post"` puts it in the request body where it belongs for anything sensitive or state-changing.*

---

## 🎯 Purpose

Every `<form>` has to answer one question before it does anything else: *how* should the browser package up the data and send it to the server? HTML gives exactly two native options, and they aren't interchangeable style choices — they have fundamentally different visibility, size, caching, and safety characteristics. Picking the wrong one can leak a password into a browser history entry, silently fail on a long form, or cause a "resubmit form?" prompt when a user hits back — all avoidable by understanding what each method is actually for.

---

## 🧠 Key Ideas

- **`method="get"`** appends form data to the URL as a query string (`?name=value&other=value2`) — the data becomes part of the address itself.
- **`method="post"`** sends form data in the **request body**, separate from the URL — the address the browser navigates to stays clean.
- GET requests are **bookmarkable and shareable** (the data travels with the URL) and have a practical length limit imposed by browsers/servers (typically a couple thousand characters) — great for searches and filters, unusable for anything large.
- GET data is **visible in the browser's address bar, history, server logs, and any place the URL gets copied or shared** — never use GET for passwords, tokens, or any sensitive data.
- POST requests are **not cached, not bookmarked, and have no practical size limit** — the correct default for anything that changes server state (creating an account, submitting a payment, posting a comment) or carries sensitive data (login credentials).

---

## ⚙️ How It Works

Picture the form's `method` attribute as choosing the "shipping label" for the data. GET writes the contents directly on the outside of the envelope — anyone who sees the envelope (the URL) sees exactly what's inside, and there's only so much room on the label before it doesn't fit. POST puts the contents inside the envelope, sealed — the outside (the URL) reveals nothing about the contents, and the envelope can hold far more.

This has a real, testable consequence: submit a GET form and watch the URL in the address bar change to include your form fields as `?field=value` pairs; submit a POST form and the URL stays as the form's `action` target, unchanged, with the data invisible in the address bar (though still fully visible to server logs, browser dev tools' Network tab, and anyone with access to the raw HTTP traffic — POST is *not encryption*, it just isn't stamped on the URL; HTTPS is what actually protects the data in transit).

The other major behavioral difference is idempotency and caching. Browsers treat GET requests as safe to cache, prefetch, and repeat without asking — which is exactly right for "search for X," since searching again for the same X should return the same result with no side effects. POST requests are treated as potentially state-changing, so browsers warn before resubmitting one (the familiar "Confirm Form Resubmission" dialog on back/refresh) rather than silently repeating something like "charge this credit card" a second time.

```html
<!-- GET: appropriate for a search — bookmarkable, shareable, no side effects -->
<form action="/search" method="get">
  <input type="text" name="q">
  <button type="submit">Search</button>
</form>
<!-- Submitting "html forms" navigates to: /search?q=html+forms -->

<!-- POST: appropriate for login — sensitive data, changes server state -->
<form action="/login" method="post">
  <input type="text" name="username">
  <input type="password" name="password">
  <button type="submit">Log In</button>
</form>
<!-- Submitting navigates to /login with credentials in the request body,
     never appearing in the URL, history, or address bar -->
```

---

## 💻 Examples

```html
<!-- GET example: a product filter — the resulting URL is meant
     to be shareable ("here's a link to blue shoes under $50") -->
<form action="/products" method="get">
  <select name="color">
    <option value="blue">Blue</option>
    <option value="red">Red</option>
  </select>
  <input type="number" name="max_price" placeholder="Max price">
  <button type="submit">Filter</button>
</form>
<!-- Result: /products?color=blue&max_price=50 -->
```

```html
<!-- POST example: creating an account — sensitive data, changes state -->
<form action="/signup" method="post">
  <label for="email">Email</label>
  <input type="email" id="email" name="email" required>

  <label for="password">Password</label>
  <input type="password" id="password" name="password" required>

  <button type="submit">Create Account</button>
</form>
<!-- Credentials never appear in the URL, browser history, or server access logs -->
```

```html
<!-- What NOT to do: never use GET for sensitive data -->
<form action="/login" method="get">
  <input type="text" name="username">
  <input type="password" name="password">
  <button type="submit">Log In</button>
</form>
<!-- Submitting sends the user to:
     /login?username=alice&password=hunter2
     — the password is now in the URL, browser history, and
     likely the server's plaintext access log, for anyone to find -->
```

---

## 🚀 Real World Applications

- Search bars, filters, and pagination almost always use GET, so users can bookmark or share a link straight to "page 3 of results" or "shoes under $50."
- Login forms, signup forms, payment forms, and comment/post-creation forms use POST, both because they contain sensitive data and because they change server state.
- E-commerce "add to cart" actions are typically POST, since repeating the request (e.g. from a refreshed page) shouldn't silently add a second item without the user meaning to.
- Any endpoint uploading a file uses POST — file contents don't fit in a URL and GET can't carry them at all.
- Analytics and tracking pixels often deliberately (ab)use GET requests for lightweight, cacheable, no-body pings — a rare case where GET's simplicity is the whole point.

---

## ⚖️ Advantages

**GET:**
- Bookmarkable, shareable, and works with the browser's back/forward cache without warnings.
- Easy to debug — the submitted data is right there in the URL.
- Cacheable by browsers and CDNs, useful for repeatable read-only operations like search.

**POST:**
- No meaningful size limit, so it handles large forms, file uploads, and rich content without truncation.
- Keeps sensitive data out of the URL, browser history, and typical server access logs.
- Browsers won't silently resubmit a POST on refresh/back without confirming — a safeguard against accidental duplicate state changes.

---

## ⚠️ Limitations

- GET's URL length limit (browser- and server-dependent, but commonly a couple thousand characters) means large forms simply won't work — data gets silently truncated or the request fails.
- POST is not itself encryption — without HTTPS, POST body data is just as interceptable in transit as a GET query string; POST protects data from appearing in the *URL*, not from network eavesdropping.
- Neither method is a security boundary on its own — both still require server-side validation, authentication, and authorization; the client-side `method` attribute is about semantics and UX, not access control.
- POST's resistance to caching means genuinely idempotent, cacheable operations mistakenly sent via POST lose out on browser/CDN caching benefits they could have had as GET.

---

## 🚨 Common Mistakes

- Using GET for a login form, exposing the password directly in the URL, browser history, and server logs.
- Using GET for any form that changes server state (like "delete this item"), which risks accidental repeats via browser prefetching, crawlers following links, or the back button.
- Assuming POST is "secure" by itself and skipping HTTPS — POST hides data from the URL, not from anyone sniffing unencrypted network traffic.
- Building a large form (many fields, file uploads, long text areas) with GET and being confused when data silently gets cut off.
- Forgetting that GET form data is visible and cacheable, then being surprised when a shared "results" link leaks a filter or search term the user assumed was private.

---

## 📖 Further Reading

- MDN: "Sending form data" (the `<form>` element's `method` attribute)
- MDN: HTTP `GET` and `POST` method references
- OWASP: guidance on not relying on GET/POST for security boundaries

---

## 💡 Wisdom from Mímir

My shortcut: if I'd be comfortable with this exact request appearing as a link in someone's browser history or a shared screenshot, GET is fine. If I'd wince at that, it's POST — full stop, no exceptions for "just a demo" or "internal tool only." I've seen more than one internal admin panel leak session tokens or passwords through server access logs simply because a login form quietly defaulted to GET.

---

## 🔗 Related Notes

- [[Forms and Form Validation]] — the broader form element this method choice lives inside
- [[Input Types and Attributes]] — how `type="password"` and other input types interact with what gets submitted
- [[The label Element and Form Accessibility]] — making the fields inside these forms usable for everyone
