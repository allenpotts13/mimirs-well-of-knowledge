---
type: dax-pattern
status: published
created: 2026-09-10
updated: 2026-09-10
technology: Power BI
difficulty: Beginner
tags:
  - dax
  - measures
  - aggregation
aliases:
  - Basic SUM Measure
publish: true
---

# <span class="rune">ᚲ</span> Creating a Basic Measure with SUM

> The first measure almost everyone writes, and the template every other aggregation measure builds on.

---

## 🎯 Problem

Show a total (revenue, quantity, count) that automatically responds to whatever filters, slicers, or visual context is currently applied — not a fixed, one-time number.

---

## 🤔 Mental Model

A measure isn't a stored value — it's a recipe, re-run fresh every time it's needed, using whatever data is currently in scope.

```text
Card visual, no filters:        SUM of every row
Card visual, "West" slicer on:  SUM of only West rows
Table by Region:                SUM recalculated once per region row
```

---

## 🧠 Why This Pattern Works

`SUM()` reads the underlying column and totals it across every row currently visible in the filter context — see [[Row Context vs Filter Context]]. Because it's a measure, not a calculated column, it recalculates automatically every time the context changes, with zero extra logic needed.

---

## 💻 DAX Solution

```dax
Total Sales = SUM(Sales[SalesAmount])
```

---

## 🔄 Step-by-Step Breakdown

### Step 1: Identify the numeric column to total
```dax
Sales[SalesAmount]
```
The raw column being aggregated.

### Step 2: Wrap it in SUM()
```dax
SUM(Sales[SalesAmount])
```
Totals that column across every row in the current filter context.

### Step 3: Name it as a measure
```dax
Total Sales = SUM(Sales[SalesAmount])
```
Now reusable anywhere in the report — cards, tables, other measures.

---

## 🚀 Common Use Cases

- Total revenue, total quantity, total cost
- The base measure other calculations (ratios, YTD, growth %) build on top of
- Any KPI card on a dashboard

---

## ⚖️ Alternatives

### `SUMX()` for row-by-row calculation before summing
```dax
Total Revenue = SUMX(Sales, Sales[Quantity] * Sales[UnitPrice])
```
Needed when the value to sum doesn't already exist as a single column — it has to be computed per row first.

### Implicit measures (dragging a column directly into a visual)
Power BI can auto-sum a numeric column dropped into a visual without an explicit measure — convenient, but not reusable, not renameable, and generally discouraged for anything beyond quick exploration.

---

## ⚠️ Performance Considerations

- `SUM()` on a plain column is one of the cheapest operations in DAX — it doesn't need row-by-row evaluation.
- Prefer `SUM()` over `SUMX()` whenever the value already exists as a column — `SUMX` iterates row by row and is more expensive.

---

## 🚨 Common Mistakes

- Using a calculated column for a total that should respond to filtering — see [[Calculated Columns vs Measures]].
- Reaching for `SUMX()` out of habit when `SUM()` would do — unnecessary row-by-row iteration for no benefit.
- Relying on implicit measures (dragging columns directly) in a model anyone else needs to maintain — explicit measures are self-documenting and reusable.

---

## 💡 Wisdom from Mímir

Every model should have a small set of explicit, named "base" measures (Total Sales, Total Cost, Total Orders) that every other measure builds from. Naming these clearly early on saves a lot of duplicated logic later.

---

## 🔗 Related Notes

- [[DAX Fundamentals]]
- [[Calculated Columns vs Measures]]
- [[Creating a Percentage of Total Measure]]
