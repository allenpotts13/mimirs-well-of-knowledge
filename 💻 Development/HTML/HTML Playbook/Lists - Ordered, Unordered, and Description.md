---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: HTML
difficulty: Beginner
tags:
  - lists
  - semantics
  - fundamentals
aliases:
  - ol ul dl
  - Description Lists
publish: true
---

# <span class="rune">ᛟ</span> Lists: Ordered, Unordered, and Description

> *Choosing between `<ol>` and `<ul>` is a decision about meaning — whether sequence matters — not about which bullet style you'd prefer to look at.*

---

## 🎯 Purpose

Lists are everywhere on the web — navigation menus, steps in a recipe, FAQ answers, ingredient lists — but "list" isn't one thing. Sometimes order is the whole point (step 3 must come after step 2). Sometimes order is irrelevant (a list of features). And sometimes what you actually have isn't a simple list at all, but a set of terms paired with their definitions. HTML gives you three different list elements because these are three genuinely different kinds of content, and picking the right one communicates that difference to browsers, screen readers, and anyone reading your markup later.

---

## 🧠 Key Ideas

- `<ol>` (ordered list) is for content where **sequence carries meaning** — steps in instructions, ranked results, anything where reordering the items would change or break their meaning.
- `<ul>` (unordered list) is for content where **order is incidental** — a set of features, a list of links, ingredients (mostly) — items that could be reshuffled without losing meaning.
- This is a **semantic choice, not a visual one** — CSS (`list-style-type`, or removing markers entirely) controls how a list *looks*; the tag you choose should reflect whether order actually matters in the content itself.
- `<dl>`/`<dt>`/`<dd>` (description list) is the underused fourth option, built for **key-value style content** — pairing a term (`<dt>`) with its description (`<dd>`), like a glossary, an FAQ, or metadata (label/value pairs).
- **Nesting a list correctly** means the nested `<ul>` or `<ol>` goes *inside* the parent `<li>` it belongs to — never as a sibling of that `<li>`, which would break the parent-child relationship the nesting is meant to express.

---

## ⚙️ How It Works

Ask yourself one question when choosing between `<ol>` and `<ul>`: *if I randomly shuffled these items, would the content still make sense?* Recipe steps, assembly instructions, and race results all fail that test — shuffle them and they're wrong or meaningless — so they're `<ol>`. A list of a product's features, a set of navigation links, or your favorite books mostly pass that test — reorder them and they're still just as valid a list — so they're `<ul>`. Screen readers announce ordered lists with their item numbers ("1 of 5, Preheat the oven"), which is exactly the extra context a sequential process needs and a features list doesn't.

`<dl>` solves a different shape of problem entirely: pairs, not a sequence of items. A glossary entry, a FAQ question and answer, or a set of product spec labels ("Weight: 2kg", "Color: Blue") are all fundamentally *term → description* pairs, not a flat list of standalone items. Wrapping these in `<ul>` with bold text for the "term" part technically displays similarly, but throws away the actual term-description relationship — a screen reader or any tool parsing the page has no way to know which text is the label and which is the value. `<dl>` keeps that pairing explicit and machine-readable.

Nesting is where a lot of otherwise-correct list markup quietly breaks. A sub-list belongs to the specific item it's a breakdown of, so it must live *inside* that `<li>`, as that item's child — not dropped after it as if it were just another item at the same level.

```html
<!-- WRONG: nested list is a sibling of the li, not inside it -->
<ul>
  <li>Fruits</li>
  <ul>
    <li>Apple</li>
    <li>Banana</li>
  </ul>
  <li>Vegetables</li>
</ul>
<!-- This breaks the outline — the nested list floats between two top-level items,
     not clearly attached to "Fruits" -->

<!-- RIGHT: nested list lives inside the li it belongs to -->
<ul>
  <li>
    Fruits
    <ul>
      <li>Apple</li>
      <li>Banana</li>
    </ul>
  </li>
  <li>Vegetables</li>
</ul>
```

---

## 💻 Examples

```html
<!-- ol: order matters -->
<h2>How to Make Coffee</h2>
<ol>
  <li>Boil water</li>
  <li>Add coffee grounds to the filter</li>
  <li>Pour water over the grounds</li>
  <li>Wait 4 minutes</li>
</ol>
```

```html
<!-- ul: order doesn't matter -->
<h2>Features</h2>
<ul>
  <li>Offline support</li>
  <li>Dark mode</li>
  <li>Cross-device sync</li>
</ul>
```

```html
<!-- dl: term/description pairs, e.g. a glossary -->
<dl>
  <dt>HTML</dt>
  <dd>The markup language used to structure content on the web.</dd>

  <dt>CSS</dt>
  <dd>The language used to style and lay out HTML content.</dd>
</dl>

<!-- dl also works well for an FAQ -->
<dl>
  <dt>Do you offer refunds?</dt>
  <dd>Yes, within 30 days of purchase.</dd>

  <dt>Is there a free trial?</dt>
  <dd>Yes, 14 days, no credit card required.</dd>
</dl>
```

```html
<!-- Correct nesting: sub-list lives inside its parent li -->
<ol>
  <li>
    Gather ingredients
    <ul>
      <li>2 cups flour</li>
      <li>1 cup sugar</li>
    </ul>
  </li>
  <li>Preheat oven to 350°F</li>
</ol>
```

---

## 🚀 Real World Applications

- Recipe sites use `<ol>` for preparation steps and often `<ul>` for the ingredient list itself, since the ingredients don't need to be gathered in a strict order but the steps do.
- FAQ sections and glossary/terminology pages are a natural fit for `<dl>`, correctly pairing each question with its answer or each term with its definition.
- Navigation menus are almost universally built from `<ul>` (`<nav><ul><li>...</li></ul></nav>`) since menu item order rarely carries the same "sequence" meaning a numbered list implies.
- Product spec sheets ("Weight: 1.2kg", "Battery life: 10hr") are a common, underused `<dl>` use case — many sites use a `<table>` or plain `<div>`s here when `<dl>` fits the label/value semantics more precisely.
- Multi-level site navigation and nested category menus rely on correctly nested `<ul>` inside `<li>` to represent parent/child category relationships.

---

## ⚖️ Advantages

- Screen readers announce list type and item count automatically ("list, 5 items"), giving users context about how much content they're about to encounter before committing to it.
- `<ol>` conveys sequence without any extra markup — no need to manually number items in the text itself.
- `<dl>` keeps term-description relationships explicit and parseable, useful for both accessibility and tools that might programmatically extract that data.
- Correct nesting keeps the DOM structure logical, which pays off in easier CSS targeting (`li > ul` selectors) and cleaner JavaScript traversal.

---

## ⚠️ Limitations

- `<dl>` is genuinely underused and less broadly recognized by developers, which sometimes makes teams reach for a `<table>` or a pair of `<div>`s out of familiarity even when `<dl>` is the better semantic fit.
- CSS list-marker styling (custom bullets, numbering formats) can require some fiddling (`::marker`, `list-style-type` values) to get exactly right across browsers.
- Very deeply nested lists become hard to read in both the markup and the rendered output — a sign the content might need restructuring rather than more nesting.

---

## 🚨 Common Mistakes

- Using `<ul>` for steps where order genuinely matters, or `<ol>` for content where it doesn't, based on visual preference for bullets vs. numbers rather than actual meaning.
- Nesting a sub-list as a sibling of the `<li>` it relates to instead of inside it, breaking the parent-child relationship.
- Reaching for `<div>`s with manual bold "labels" instead of `<dl>`/`<dt>`/`<dd>` for genuinely paired term/value content.
- Using an `<ol>`/`<ul>` purely to get default spacing or bullet styling on content that isn't actually a list at all (e.g. a single paragraph split into fake "list items").
- Forgetting that `<li>` elements must be direct children of `<ul>`, `<ol>`, or `<menu>` — placing other elements between the list and its items breaks the list structure.

---

## 📖 Further Reading

- MDN: "`<ol>`: The Ordered List element"
- MDN: "`<dl>`: The Description List element"
- MDN: "HTML lists" (Learn HTML)

---

## 💡 Wisdom from Mímir

The test I actually use is embarrassingly simple: I imagine shuffling the items like a deck of cards. If the content survives that shuffle with its meaning intact, it's a `<ul>`. If shuffling it would visibly break something, it's an `<ol>`. And if what I'm looking at isn't really "items" at all but pairs of label-and-value, I stop trying to force it into either and reach for `<dl>` instead — it's the list type most people forget exists, and it's usually exactly the tool the content was asking for.

---

## 🔗 Related Notes

- [[Semantic HTML and Why It Matters]] — the same meaning-over-appearance principle applied specifically to list choice
- [[Tables for Tabular Data]] — another structured-content element sometimes confused with, or misused in place of, a list
- [[Accessibility Basics - alt Text, Labels, and ARIA]] — how screen readers announce list type and item counts to give users navigational context
