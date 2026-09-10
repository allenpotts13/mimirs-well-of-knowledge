---
type: dax-pattern
status: published
created: 2026-09-10
updated: 2026-09-10
technology: Power BI
difficulty: Intermediate
tags:
  - dax
  - calculate
  - filter
aliases:
  - CALCULATE FILTER Pattern
publish: true
---

# <span class="rune">ᚲ</span> Filtering a Measure with CALCULATE and FILTER

> Override or add to a report's existing filters with a specific, custom condition — the workhorse pattern behind most "compare this segment to that segment" measures.

---

## 🎯 Problem

Show a total that applies a specific condition — regardless of, or in addition to, whatever the report's own slicers and filters are currently doing.

---

## 🤔 Mental Model

```text
CALCULATE(expression, filter)
              ↑
   replaces any existing filter on that same column,
   then evaluates the expression against the new filter
```

---

## 🧠 Why This Pattern Works

`CALCULATE()`'s filter arguments can be a simple boolean condition on a column, or a full table produced by `FILTER()` when the condition is more complex than a single column comparison. `FILTER()` iterates row by row over a table, keeping only rows matching the condition — that filtered table then becomes the new filter context for the wrapped expression.

---

## 💻 DAX Solution

```dax
// Simple boolean filter — most common case
High Value Sales =
CALCULATE(
	[Total Sales],
	Sales[SalesAmount] > 1000
)

// FILTER() for conditions across multiple columns or complex logic
Large West Region Orders =
CALCULATE(
	[Total Sales],
	FILTER(
		Sales,
		Sales[Region] = "West" && Sales[SalesAmount] > 1000
	)
)
```

---

## 🔄 Step-by-Step Breakdown

### Step 1: Identify the base measure to filter
```dax
[Total Sales]
```

### Step 2: Choose a simple boolean filter, or FILTER() for anything more complex
A single-column condition can go directly as a `CALCULATE` argument. Multi-column or row-by-row logic needs `FILTER()`.

### Step 3: Wrap it all in CALCULATE
```dax
CALCULATE([Total Sales], <filter>)
```
Remember: this filter **replaces** any existing filter on the same column(s), it doesn't add to it — see [[The CALCULATE Function]].

---

## 🚀 Common Use Cases

- "Sales over $1,000" or any threshold-based segment
- Comparing one specific category/region against the overall filtered view
- Multi-condition business rules that don't map to a single column filter

---

## ⚖️ Alternatives

### `KEEPFILTERS()` — add to, rather than replace, an existing filter
```dax
CALCULATE(
	[Total Sales],
	KEEPFILTERS(Sales[Region] = "West")
)
```
Use when the filter should combine with the report's existing filter instead of overriding it.

### Simple boolean filter instead of FILTER()
When the condition is on a single column, skip `FILTER()` entirely — `CALCULATE([Total Sales], Sales[Region] = "West")` is simpler and just as correct as wrapping it in `FILTER()`.

---

## ⚠️ Performance Considerations

- `FILTER()` iterates the table row by row — cheap on small dimension tables, potentially expensive on a large fact table. Prefer simple boolean `CALCULATE` filters when the condition allows it.
- Filtering on a column with a bitmap-friendly cardinality (few distinct values) is generally faster than filtering on high-cardinality columns.

---

## 🚨 Common Mistakes

- Reaching for `FILTER()` for a condition that's really just a simple single-column boolean — adds unnecessary iteration cost.
- Forgetting the filter replaces, rather than combines with, the existing report filter on that column — see [[The CALCULATE Function]] for the fix (`KEEPFILTERS`).
- Writing `FILTER(Sales, ...)` when a smaller related table would be more efficient to filter on instead.

---

## 💡 Wisdom from Mímir

Reach for the simplest form first — a plain boolean condition directly inside `CALCULATE()`. Only bring in `FILTER()` once the condition genuinely needs row-by-row logic across multiple columns that a single boolean expression can't express.

---

## 🔗 Related Notes

- [[The CALCULATE Function]]
- [[DAX Fundamentals]]
- [[Row Context vs Filter Context]]
