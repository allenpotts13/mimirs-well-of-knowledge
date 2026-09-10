---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-06
technology: JavaScript
difficulty: Intermediate
tags:
  - error-handling
  - try-catch
  - debugging
  - control-flow
aliases:
  - Error Handling with try/catch
  - try/catch/finally
publish: true
---

# <span class="rune">ᛟ</span> Error Handling with try/catch

> *`try`/`catch` lets you attempt a risky piece of code and gracefully handle whatever goes wrong, instead of letting the whole program crash.*

---

## 🎯 Purpose

Things fail. A network request times out, a user pastes malformed JSON into a text box, a function gets called with `undefined` instead of the object it expected. Without a way to handle failure, one bad line of code takes down everything after it — the script just stops. `try`/`catch` exists to give you a controlled way to say "attempt this, and if it blows up, here's what to do instead of crashing." It's the difference between a program that degrades gracefully and one that dies the first time reality doesn't match your assumptions.

---

## 🧠 Key Ideas

- A `try` block wraps code that *might* throw an error; if it does, execution immediately jumps to the matching `catch` block instead of continuing.
- The `catch` block receives the thrown value (conventionally an `Error` object) as a parameter, letting you inspect what went wrong and decide how to respond.
- `finally` is an optional third block that runs **no matter what** — whether the `try` succeeded, an error was caught, or the function returned early from inside either block. It's for cleanup that must always happen (closing a file, hiding a loading spinner, releasing a lock).
- You should `throw new Error("message")` rather than `throw "message"`. A real `Error` object captures a stack trace and exposes a consistent `.message` and `.name`, which makes debugging and logging dramatically easier than chasing a bare string through the call stack.
- **The big gotcha:** a synchronous `try`/`catch` only catches errors thrown *synchronously, within that block's execution*. An error thrown inside a `setTimeout` callback, a DOM event handler, or an unhandled/un-awaited Promise happens on a completely different turn of the event loop — by the time it throws, the original `try` block has already finished and returned. That `catch` is not there to catch it, and the error escapes as an uncaught exception (or a "Uncaught (in promise)" rejection) instead.
- Custom error types (`class ValidationError extends Error { ... }`) let you `catch` a general error and then branch on `error instanceof ValidationError` to handle different failure categories differently.

---

## ⚙️ How It Works

Think of `try`/`catch` as a safety net strung under a tightrope walker. The tightrope walker (your `try` block) does their normal routine, one statement at a time. If they slip — an error is thrown, whether by your code (`throw`) or by the JavaScript engine itself (calling a method on `undefined`, for instance) — they don't hit the ground and end the show. They fall into the net (`catch`), and the show continues from there instead of stopping cold.

Crucially, the net is only strung under *that one tightrope*. If a completely different act is happening on a different stage at a different time — like a `setTimeout` callback firing two seconds later — that safety net isn't there anymore. The `try` block has already finished executing (there was nothing left inside it to wait for), so any error thrown later, asynchronously, falls with no net underneath it at all.

This is why the fix for async code is to bring the `await` *inside* the `try` block. `await` pauses the `async` function at that line until the Promise settles, which means the function is still "inside" the `try` when the rejection happens — so the net is still there to catch it. A bare `.then()`/`.catch()` chain, or a `setTimeout` callback that isn't awaited, has already left the `try` block behind by the time it runs.

`finally` is the stagehand who always comes out to sweep the stage after the act, whether it went perfectly, ended in a fall into the net, or the performer walked off early. It runs regardless of the outcome — even if the `try` or `catch` block has a `return` statement, `finally` still executes before that return actually completes (though a `return` inside `finally` itself will override any earlier return, which is a subtlety worth knowing and mostly avoiding).

---

## 💻 Examples

```javascript
// Basic try/catch/finally
function parseUserAge(input) {
  try {
    const age = JSON.parse(input); // throws SyntaxError on bad JSON
    if (typeof age !== "number" || age < 0) {
      throw new Error("Age must be a non-negative number");
    }
    return age;
  } catch (error) {
    console.error("Failed to parse age:", error.message);
    return null; // fall back to a sane default instead of crashing
  } finally {
    console.log("parseUserAge attempt finished"); // always runs
  }
}

parseUserAge("29");        // logs "parseUserAge attempt finished", returns 29
parseUserAge("not json");  // logs the SyntaxError message, then the finally, returns null
parseUserAge("-5");        // logs "Age must be a non-negative number", then finally, returns null
```

```javascript
// Throwing a real Error object vs. a plain string
function withdraw(balance, amount) {
  if (amount > balance) {
    // Good: preserves a stack trace, a .message, and a .name of "Error"
    throw new Error(`Insufficient funds: tried to withdraw ${amount} from ${balance}`);
  }
  return balance - amount;
}

try {
  withdraw(100, 250);
} catch (error) {
  console.log(error.name);    // "Error"
  console.log(error.message); // "Insufficient funds: tried to withdraw 250 from 100"
  console.log(error.stack);   // full stack trace pointing at the throw site
}

// Bad: throwing a plain string loses all of that
// throw "Insufficient funds"; // error.message, error.name, error.stack don't exist
```

```javascript
// Custom error classes for branching logic
class ValidationError extends Error {
  constructor(message) {
    super(message);
    this.name = "ValidationError";
  }
}

function createUser(data) {
  if (!data.email) {
    throw new ValidationError("Email is required");
  }
  // ...proceed with creation
}

try {
  createUser({});
} catch (error) {
  if (error instanceof ValidationError) {
    console.log("Show this to the user:", error.message);
  } else {
    console.log("Unexpected error — log it and alert the team:", error);
  }
}
```

```javascript
// THE GOTCHA: try/catch does NOT catch errors from async callbacks
function riskyTimeout() {
  try {
    setTimeout(() => {
      throw new Error("Boom, thrown a second later");
    }, 1000);
  } catch (error) {
    // This NEVER runs. By the time the timeout fires, the try block
    // already finished executing and returned control to the caller.
    console.log("Caught it!", error);
  }
}
riskyTimeout(); // logs an uncaught exception in the console a second later — the catch never fires

// The fix: put the try/catch INSIDE the async code itself,
// or await the async operation so it's still "inside" the try.
async function fetchData(url) {
  try {
    const response = await fetch(url); // await keeps us inside the try
    const data = await response.json();
    return data;
  } catch (error) {
    // THIS works, because we awaited the failure point while still inside try
    console.error("Fetch failed:", error.message);
    return null;
  }
}
```

---

## 🚀 Real World Applications

- Wrapping `JSON.parse()` calls on data from an API, `localStorage`, or user input, all of which can hand you malformed JSON.
- Catching network failures around `fetch()` or database calls so the app can show a friendly "something went wrong, try again" message instead of a blank screen.
- Validating user input in a form submission handler and throwing custom errors that map directly to field-level error messages.
- Wrapping third-party library calls you don't fully trust, so a bug in a dependency doesn't take down your whole application.
- Cleanup with `finally` — releasing a database connection, closing a file handle, or hiding a loading spinner regardless of whether the operation succeeded.

---

## ⚖️ Advantages

- Prevents one failure from crashing the entire script — execution can continue past the point of failure.
- Centralizes error-handling logic instead of scattering defensive `if` checks before every risky operation.
- Custom `Error` subclasses give you structured, inspectable failures instead of guessing based on string matching.
- `finally` guarantees cleanup code runs, which is hard to reliably achieve any other way given early returns and thrown errors.

---

## ⚠️ Limitations

- It cannot catch errors thrown asynchronously outside the `try` block's actual execution window (see the gotcha above) — this trips up even experienced developers.
- Overusing `try`/`catch` to swallow errors silently (an empty `catch` block) hides real bugs and makes debugging much harder later.
- It adds a small amount of visual and cognitive overhead; wrapping every single line defensively instead of only the genuinely risky ones makes code harder to read.
- `catch` blocks that don't check `error instanceof SomeSpecificError` end up treating totally unrelated failures (a typo causing a `TypeError`, say) the same as the expected failure case you were guarding against.

---

## 🚨 Common Mistakes

- Assuming `try`/`catch` around a function call will catch errors thrown later inside a `setTimeout`, a Promise `.then()`, or an event listener registered by that function — it won't, unless you `await` inside the `try`.
- Throwing plain strings or objects instead of `Error` instances, losing the stack trace that would have told you exactly where things went wrong.
- Writing an empty `catch (error) {}` block that silently swallows the problem — the code "works" until the swallowed error was actually load-bearing.
- Forgetting that a `return` inside `finally` silently overrides a `return` from the `try` or `catch` block — a rare but genuinely confusing bug.
- Catching an error broadly and then re-throwing a *new, less informative* error, destroying the original stack trace and message in the process.

---

## 📖 Further Reading

- MDN: "try...catch"
- MDN: "Error" and "Error.prototype.stack"
- *You Don't Know JS: Async & Performance* (Kyle Simpson) — the chapters on error handling in async code

---

## 💡 Wisdom from Mímir

The single biggest "aha" I can hand you about `try`/`catch` is this: it's not really about the *code inside the braces*, it's about the *call stack that's currently active*. A `try` block only catches what happens while it is still on the stack, waiting. `await` is what lets async code politely wait its turn back on that stack — a `setTimeout` callback or a stray `.then()` never gets that courtesy, so it throws into empty air. Once that clicks, the whole "why didn't my catch fire" mystery evaporates for good.

---

## 🔗 Related Notes

- [[Promises]]
- [[Async-Await]]
- [[The Event Loop - Call Stack, Task Queue, and Microtasks]]
- [[The Fetch API for HTTP Requests]]
