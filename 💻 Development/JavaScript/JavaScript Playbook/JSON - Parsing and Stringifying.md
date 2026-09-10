---
type: concept
status: active
created: 2026-09-02
updated: 2026-09-06
technology: JavaScript
difficulty: Beginner
tags:
  - json
  - serialization
  - parsing
  - data-formats
aliases:
  - "JSON: Parsing and Stringifying"
  - JSON.parse
  - JSON.stringify
---

# 📚 JSON: Parsing and Stringifying

> *`JSON.stringify()` turns a JavaScript value into a JSON text string; `JSON.parse()` turns a JSON text string back into a JavaScript value — and the two are not always perfect mirror images of each other.*

---

## 🎯 Purpose

JavaScript values live in memory as objects, arrays, numbers, and so on — but the moment you need to send data over a network, save it to a file, or store it in `localStorage`, you need it as plain **text**. JSON (JavaScript Object Notation) is the near-universal text format for that job, and `JSON.stringify`/`JSON.parse` are the built-in tools for converting between the two. This concept exists because that conversion is used constantly — every `fetch` call that sends or receives JSON, every value saved to browser storage — and because a few of its rules (what gets silently dropped, what doesn't survive a round trip) are easy to be caught off guard by.

---

## 🧠 Key Ideas

- **`JSON.stringify(value)`** converts a JavaScript value into a JSON-formatted **string**. This is what you send over the network or save to storage.
- **`JSON.parse(string)`** converts a JSON-formatted string back into a live JavaScript value (object, array, number, string, boolean, or `null`).
- JSON only supports a small set of types: objects, arrays, strings, numbers, booleans, and `null`. Anything outside that set gets special (and sometimes surprising) treatment during `stringify`.
- **`undefined` values and functions are dropped entirely** by `stringify` — if an object property's value is `undefined` or a function, that key disappears from the output completely (inside an array, it becomes `null` instead of disappearing, to preserve array length/indices).
- **`Date` objects are converted to plain ISO-8601 strings** by `stringify` (via their `toJSON` method), and `parse` does **not** automatically convert them back into `Date` objects — you get a plain string on the other end unless you convert it yourself.
- Both methods accept an optional second argument — a **`replacer`** for `stringify` (a function or array to filter/transform properties as they're serialized) and a **`reviver`** for `parse` (a function that can transform values as they're being parsed back in) — useful for exactly this kind of custom round-tripping.

---

## ⚙️ How It Works

Think of `JSON.stringify` as photographing a JavaScript value: it only captures what fits inside the "JSON camera's" supported types. Anything the camera doesn't know how to represent — a function, an `undefined`, a `Symbol` — just doesn't show up in the photo. A `Date` object gets converted into a plain descriptive caption (its ISO string) rather than being captured as a "living" date object, because JSON itself has no concept of a date type at all — it only knows strings, numbers, booleans, objects, arrays, and `null`.

`JSON.parse` then works purely from that photograph — the plain text — and has no way of knowing that a particular string used to be a `Date` before it was flattened. It reconstructs exactly what the JSON text describes: a string is a string, an object is an object. Getting a real `Date` object back out requires you to explicitly say "treat this string as a date," either manually after parsing or via a `reviver` function.

---

## 💻 Examples

```javascript
// Basic round trip
const user = { name: "Freya", age: 30, isAdmin: true };

const jsonString = JSON.stringify(user);
console.log(jsonString);
// '{"name":"Freya","age":30,"isAdmin":true}'  — note: it's a STRING now

const parsedBack = JSON.parse(jsonString);
console.log(parsedBack);       // { name: 'Freya', age: 30, isAdmin: true }
console.log(parsedBack.age);   // 30 — a real number again, not a string
```

```javascript
// What silently disappears: undefined values and functions
const data = {
  name: "Loki",
  role: undefined,               // will be DROPPED
  greet: function () { return "hi"; }, // will be DROPPED
  age: 1000,
};

console.log(JSON.stringify(data));
// '{"name":"Loki","age":1000}'  — role and greet are just gone, no error, no warning

// Inside an array, undefined/functions become null instead of vanishing,
// to keep the array's length and index positions intact:
console.log(JSON.stringify([1, undefined, function () {}, 4]));
// '[1,null,null,4]'
```

```javascript
// Dates: they survive stringify as ISO strings, but DON'T come back as Dates
const event = { title: "Ragnarok Planning", when: new Date("2026-12-01") };

const serialized = JSON.stringify(event);
console.log(serialized);
// '{"title":"Ragnarok Planning","when":"2026-12-01T00:00:00.000Z"}'

const restored = JSON.parse(serialized);
console.log(typeof restored.when);        // "string" — NOT a Date object!
console.log(restored.when instanceof Date); // false

// You have to convert it back yourself:
const trueDate = new Date(restored.when);
console.log(trueDate instanceof Date); // true
```

```javascript
// Using a reviver to automatically convert date-looking strings back into Dates
const restoredWithReviver = JSON.parse(serialized, (key, value) => {
  if (key === "when") return new Date(value);
  return value;
});
console.log(restoredWithReviver.when instanceof Date); // true
```

```javascript
// Using a replacer to filter or transform what gets serialized
const secretUser = { name: "Baldr", password: "hunter2", age: 25 };

const safeJson = JSON.stringify(secretUser, (key, value) => {
  if (key === "password") return undefined; // omit this key entirely
  return value;
});
console.log(safeJson); // '{"name":"Baldr","age":25}'

// A replacer array is a shorthand allowlist of keys to keep:
console.log(JSON.stringify(secretUser, ["name", "age"]));
// '{"name":"Baldr","age":25}'

// A third argument controls pretty-printing (indentation):
console.log(JSON.stringify({ a: 1, b: 2 }, null, 2));
// {
//   "a": 1,
//   "b": 2
// }
```

---

## 🚀 Real World Applications

- **Sending/receiving data with `fetch`**: `JSON.stringify(body)` before sending a POST request, and `await response.json()` (which does the `JSON.parse` internally) when reading a response.
- **Persisting state to `localStorage`**: since Web Storage only stores strings, saving an object or array requires `JSON.stringify` before writing and `JSON.parse` after reading it back.
- **Deep-cloning simple objects**: `JSON.parse(JSON.stringify(obj))` is a widely-used (if imperfect) quick way to deep-clone plain data — it fails for `Date`s, functions, `undefined`, and circular references, which is exactly why it's a "quick" trick and not a general-purpose solution.
- **Config files and API payloads**: JSON is the default format for REST API bodies, `package.json`, and countless config files, all read/written through these two functions.
- **Redacting sensitive fields before logging**: a `replacer` function is a clean way to strip out passwords, tokens, or other sensitive fields before writing an object to logs.

---

## ⚖️ Advantages

- Built into the language with zero dependencies, and available in every modern JS environment (browser and Node alike).
- Human-readable text format that's also easy for other languages/systems to parse, making it the closest thing to a universal data-interchange format.
- The `replacer`/`reviver` hooks give you real, if manual, control over exactly what gets serialized and how it's reconstructed.

---

## ⚠️ Limitations

- JSON has no concept of `Date`, `Map`, `Set`, `undefined`, functions, or `Symbol` — all of these either disappear or get flattened into a plain type during `stringify`.
- Circular references (an object that refers back to itself, directly or indirectly) throw a `TypeError` when passed to `JSON.stringify` — there's no built-in handling for them.
- Large numbers beyond `Number.MAX_SAFE_INTEGER` and all `BigInt` values cannot be serialized by `JSON.stringify` at all (`BigInt` throws a `TypeError`); precision can silently be lost for very large regular numbers.
- Key order in objects isn't formally guaranteed by the JSON spec, though in practice modern JS engines preserve insertion order for string keys.

---

## 🚨 Common Mistakes

- Assuming a value survived a round trip through `JSON.stringify`/`JSON.parse` unchanged, then being confused later when `typeof restoredDate === "string"` instead of a `Date` object.
- Forgetting that `undefined` properties just vanish silently — no error is thrown, which can hide bugs where a property was expected to exist but quietly isn't there after serialization.
- Relying on `JSON.parse(JSON.stringify(obj))` as a general-purpose deep clone and being surprised when it loses `Date`s, functions, or throws on circular references — `structuredClone()` is the modern, more capable alternative for actual deep cloning.
- Passing a `BigInt` to `JSON.stringify` and getting a `TypeError: Do not know how to serialize a BigInt` — it must be converted to a `String` or `Number` first (with the usual precision caveats for `Number`).
- Trying to `JSON.stringify` an object with a circular reference (e.g. a DOM node with a `parentNode` back-reference) and hitting `TypeError: Converting circular structure to JSON`.

---

## 📖 Further Reading

- MDN: "JSON.stringify()"
- MDN: "JSON.parse()"
- MDN: "JSON" (overview of the format itself, and its relationship to JavaScript object literals)

---

## 💡 Wisdom from Mímir

The single most useful mental model here is: **JSON is a lossy photograph, not a perfect backup.** It only speaks six types — object, array, string, number, boolean, and `null` — so anything richer than that gets flattened, dropped, or silently reinterpreted on the way through. Once that clicks, none of the "gotchas" in this note are surprising anymore; they're just consequences of the format's limited vocabulary.

My rule of thumb after being bitten by the `Date`-becomes-a-string issue more than once: any time an object crosses a serialization boundary — an API call, `localStorage`, a worker message — assume every `Date` on the other side needs to be manually reconstructed with `new Date(...)`, and check for it explicitly rather than trusting `instanceof Date` to still be true.

---

## 🔗 Related Notes

- [[Local Storage vs Session Storage]] — the most common place developers reach for `JSON.stringify`/`JSON.parse` outside of network requests
- [[The Fetch API for HTTP Requests]] — where JSON serialization is used constantly to send and receive request bodies
- [[Object Destructuring and the Spread-Rest Operators]] — often used together with parsed JSON data to pull out specific fields
