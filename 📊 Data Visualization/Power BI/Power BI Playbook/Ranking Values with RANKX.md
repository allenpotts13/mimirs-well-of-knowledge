---
type: dax-pattern
status: published
created: 2026-09-10
updated: 2026-09-10
technology: Power BI
difficulty: Intermediate
tags:
  - dax
  - ranking
aliases:
  - RANKX
publish: true
---

# <span class="rune">ᚲ</span> Ranking Values with RANKX

> Assign each row a rank relative to the rest of the table — the DAX equivalent of a SQL window function ranking.

---

## 🎯 Problem

Show each product's (or salesperson's, or region's) rank by total sales, updating dynamically as filters change.

---

## 🤔 Mental Model

```text
Product   Sales   Rank
Widget A   500     1
Widget B   400     2
Widget C   200     3
```

Each row's rank is computed relative to every other row in a defined comparison set — that set can shrink or grow as filters change.

---

## 🧠 Why This Pattern Works

`RANKX()` evaluates an expression for every row in a table, then ranks the current row's value against all the others in that same table. Because it's a measure, the ranking automatically recalculates for whatever's currently filtered — ranking within a region, within a year, or across the whole dataset.

---

## 💻 DAX Solution

```dax
Product Sales Rank =
RANKX(
	ALL(Products[ProductName]),
	[Total Sales]
)
```

---

## 🔄 Step-by-Step Breakdown

### Step 1: Define the comparison set
```dax
ALL(Products[ProductName])
```
`ALL()` removes the existing filter so every product is included in the ranking comparison, not just the one currently in the row/filter context.

### Step 2: Define the value to rank by
```dax
[Total Sales]
```
`RANKX` re-evaluates this measure for each product in the comparison set.

### Step 3: Combine into RANKX
```dax
RANKX(ALL(Products[ProductName]), [Total Sales])
```
Returns the current row's position within that ranked set.

---

## 🚀 Common Use Cases

- Top-performing product, salesperson, or region leaderboards
- "This region ranks #3 out of 10" style KPI cards
- Highlighting the top N rows in a table visual with conditional formatting based on the rank measure

---

## ⚖️ Alternatives

### Ranking within a group (partitioned ranking)
```dax
Rank Within Category =
RANKX(
	ALL(Products[ProductName]),
	[Total Sales],
	,
	DESC,
	Dense
)
```
Adding the optional 4th/5th arguments controls sort direction and tie-handling — mirrors the choice between `RANK()` and `DENSE_RANK()` in SQL's [[Window Functions]].

### TOPN() for just the top N rows, without a full ranking column
```dax
Top 5 Products = TOPN(5, ALL(Products), [Total Sales])
```
Use when only the top N rows themselves are needed, not a rank number for every row.

---

## ⚠️ Performance Considerations

- `RANKX` re-evaluates the ranking expression for every row in the comparison set, every time — can get expensive on a large table with a complex measure being ranked.
- Narrowing the comparison set with `ALL()` on just the needed column (not the whole table) keeps the evaluation smaller.

---

## 🚨 Common Mistakes

- Forgetting `ALL()`, leaving the existing filter in place — this collapses the comparison set down to just the current row, making every rank come out as 1.
- Not specifying tie-handling, leading to unexpected gaps or non-gaps in the ranking depending on the default behavior.
- Ranking against the wrong grain — ranking individual transactions instead of aggregated totals per entity.

---

## 💡 Wisdom from Mímir

If every row shows rank 1, the almost-certain cause is a missing `ALL()` — the comparison set collapsed down to a single row before the ranking even happened. This is the single most common `RANKX` mistake.

---

## 🔗 Related Notes

- [[DAX Fundamentals]]
- [[The CALCULATE Function]]
- [[Window Functions]]
