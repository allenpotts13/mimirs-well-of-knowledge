---
type: power-query-pattern
status: published
created: 2026-09-10
updated: 2026-09-10
technology: Power BI
difficulty: Beginner
tags:
  - power-query
  - conditional-logic
aliases:
  - Power Query Conditional Column
publish: true
permalink: power-bi/adding-a-conditional-column-in-power-query
---

# <span class="rune">ᚲ</span> Adding a Conditional Column in Power Query

> Bucket or label rows based on a condition, at the data-prep stage — before the model, before DAX, using a point-and-click UI.

---

## 🎯 Problem

A new column is needed that categorizes each row based on a condition (price tier, status label, region grouping) — and it belongs in the data itself, not recalculated repeatedly in every report as DAX.

---

## 🤔 Mental Model

```text
IF SalesAmount >= 10000  THEN "Platinum"
ELSE IF SalesAmount >= 5000  THEN "Gold"
ELSE IF SalesAmount >= 1000  THEN "Silver"
ELSE "Bronze"
```

Same logic as [[Using SWITCH for Multiple Conditions|SWITCH() in DAX]], but built once during data prep instead of evaluated repeatedly at report time.

---

## 🧠 Why This Pattern Works

A conditional column becomes a real, stored column in the query's output — computed once per refresh, not re-evaluated per visual interaction the way a DAX measure would be. This is the right home for logic that doesn't need to respond to report-time filtering.

---

## 🔄 Power Query Steps

### Step 1: Open the Add Column tab, choose Conditional Column
`Add Column` → `Conditional Column`.

### Step 2: Define each condition in order
Set the column to test, the operator, the comparison value, and the output for that branch — add as many `Else If` clauses as needed.

### Step 3: Set the final Else (fallback) value
Covers every row that doesn't match any of the defined conditions.

### Step 4: Confirm — Power Query generates the underlying M automatically

---

## 💻 Underlying M Code

```m
= Table.AddColumn(
	PreviousStep,
	"Sales Tier",
	each
		if [SalesAmount] >= 10000 then "Platinum"
		else if [SalesAmount] >= 5000 then "Gold"
		else if [SalesAmount] >= 1000 then "Silver"
		else "Bronze"
)
```

---

## 🚀 Common Use Cases

- Categorizing rows into tiers or buckets during data prep
- Standardizing inconsistent status/category text values into clean labels
- Flagging rows that meet a business rule (e.g. "High Risk") before the model layer

---

## ⚖️ Alternatives

### DAX `SWITCH()` or calculated column, done at the model layer instead
See [[Using SWITCH for Multiple Conditions]] — the right choice when the categorization needs to respond dynamically to filters, or depends on a measure rather than a raw source column.

### Custom Column with full M code
`Add Column` → `Custom Column`, writing the `if/then/else` M expression directly — same result, more flexible for complex conditions the point-and-click UI can't express.

---

## ⚠️ Performance Considerations

- A conditional column generally folds well against database sources, since `if/then/else` logic usually translates cleanly into a SQL `CASE` expression — see [[Query Folding]].
- Prefer this over an equivalent DAX calculated column when the underlying value is a static, row-level fact — it keeps the model layer lighter.

---

## 🚨 Common Mistakes

- Building the same categorization logic in both Power Query and DAX in different reports, instead of centralizing it once, upstream.
- Ordering conditions incorrectly — like [[Using SWITCH for Multiple Conditions|SWITCH()]], the first matching condition wins, so broad conditions need to come after specific ones, not before.
- Forgetting a final Else value, leaving unmatched rows null instead of a sensible default.

---

## 💡 Wisdom from Mímir

If the categorization depends only on raw source data (not on anything the user filters or selects at report time), it almost always belongs in Power Query, not DAX. Doing it here means it's computed once per refresh instead of repeatedly at query time in every visual.

---

## 🔗 Related Notes

- [[Power Query (M) Basics]]
- [[Using SWITCH for Multiple Conditions]]
- [[Query Folding]]
