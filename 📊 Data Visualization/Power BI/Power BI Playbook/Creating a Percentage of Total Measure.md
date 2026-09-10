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
  - aggregation
aliases:
  - Percent of Total
  - Percent of Parent
publish: true
---

# <span class="rune">ᚲ</span> Creating a Percentage of Total Measure

> Show what share of the grand total each row represents — one of the most requested measures in any report, and a clean showcase of what `ALL()` is for.

---

## 🎯 Problem

Show each category's (region, product, rep) value as a percentage of the overall total — ignoring whatever filter is currently narrowing that specific row down.

---

## 🤔 Mental Model

```text
Region     Sales    % of Total
West       400      40%
East       350      35%
South      250      25%
                     ─────
Grand Total 1000    100%
```

Each row's percentage needs two numbers: its own filtered total, and the *unfiltered* grand total.

---

## 🧠 Why This Pattern Works

The numerator ([Total Sales]) naturally respects whatever filter context the current row is in. The denominator needs to deliberately ignore that same filter — `ALL()` strips it out inside a nested `CALCULATE()`, producing the true grand total regardless of which row is being evaluated.

---

## 💻 DAX Solution

```dax
Percent of Total =
DIVIDE(
	[Total Sales],
	CALCULATE([Total Sales], ALL(Sales))
)
```

---

## 🔄 Step-by-Step Breakdown

### Step 1: Start with the normal, filtered total
```dax
[Total Sales]
```
This respects whatever row/category the measure is currently evaluated for.

### Step 2: Compute the grand total, ignoring filters
```dax
CALCULATE([Total Sales], ALL(Sales))
```
`ALL(Sales)` removes every filter on the `Sales` table, so this always returns the same grand total no matter which row it's evaluated in.

### Step 3: Divide safely
```dax
DIVIDE(numerator, denominator)
```
See [[Handling Blank Values with DAX]] for why `DIVIDE()` beats raw division here.

---

## 🚀 Common Use Cases

- "% of total revenue by region" tables and charts
- Market share style breakdowns
- Contribution analysis (which products drive the most revenue)

---

## ⚖️ Alternatives

### Percent of parent (not grand total) — narrower ALL() scope
```dax
Percent of Category =
DIVIDE(
	[Total Sales],
	CALCULATE([Total Sales], ALL(Products[ProductName]))
)
```
Removes the filter on just one column (product name within its category) instead of the whole table, producing "share of category" instead of "share of everything."

### `ALLSELECTED()` — respects slicers, ignores only the visual's own row/column filters
```dax
Percent of Slicer-Filtered Total =
DIVIDE(
	[Total Sales],
	CALCULATE([Total Sales], ALLSELECTED(Sales))
)
```
Useful when the percentage should reflect whatever's selected in slicers, but still ignore the specific row breakdown within a table/matrix visual.

---

## ⚠️ Performance Considerations

- `ALL()` on a large fact table forces a full re-scan ignoring existing filters — generally fine, but can add up in a large matrix with many rows.
- Scoping `ALL()` to just the specific column needed (rather than the entire table) is both more precise and usually cheaper to evaluate.

---

## 🚨 Common Mistakes

- Using `ALL(Sales)` (whole table) when only `ALL(Products[Category])` was intended — this silently changes "percent of category" into "percent of everything."
- Forgetting `DIVIDE()`, risking a division-by-zero error on any row where the grand total happens to be blank.
- Confusing `ALL()` (ignore every filter, including slicers) with `ALLSELECTED()` (respect slicers, only ignore the visual's internal breakdown) — picking the wrong one produces a percentage relative to the wrong base.

---

## 💡 Wisdom from Mímir

Before writing the measure, say out loud exactly what the denominator should represent — the true grand total? The category total? Whatever's currently selected in the slicers? That sentence picks between `ALL()`, a scoped `ALL()`, and `ALLSELECTED()` immediately.

---

## 🔗 Related Notes

- [[The CALCULATE Function]]
- [[Handling Blank Values with DAX]]
- [[Creating a Basic Measure with SUM]]
