---
type: dax-pattern
status: published
created: 2026-09-10
updated: 2026-09-10
technology: Power BI
difficulty: Beginner
tags:
  - dax
  - conditional-logic
aliases:
  - SWITCH
  - DAX CASE
publish: true
---

# <span class="rune">ᚲ</span> Using SWITCH for Multiple Conditions

> DAX's cleaner alternative to a long chain of nested IF() statements — the equivalent of SQL's [[Bucketing Values Into Ranges With CASE|CASE expression]].

---

## 🎯 Problem

Categorize a value into one of several buckets, or branch logic across more than two outcomes, without a deeply nested and hard-to-read chain of `IF()` calls.

---

## 🤔 Mental Model

```text
SWITCH(value,
	option1, result1,
	option2, result2,
	...
	default_result
)
```

Reads top to bottom like a lookup table — first matching option wins.

---

## 🧠 Why This Pattern Works

`SWITCH()` evaluates an expression once, then compares it against a list of possible values in order, returning the result tied to the first match. This avoids the readability problem of stacking `IF(IF(IF(...)))`, and avoids re-evaluating the same expression repeatedly the way nested `IF()`s sometimes require.

---

## 💻 DAX Solution

```dax
Sales Tier =
SWITCH(
	TRUE(),
	[Total Sales] >= 10000, "Platinum",
	[Total Sales] >= 5000, "Gold",
	[Total Sales] >= 1000, "Silver",
	"Bronze"
)
```

---

## 🔄 Step-by-Step Breakdown

### Step 1: Decide what's being matched
For exact value matching, use `SWITCH([Column], ...)`. For range/threshold logic, use `SWITCH(TRUE(), <condition1>, ..., <condition2>, ...)` — the `TRUE()` pattern lets each branch be its own boolean condition instead of an exact match.

### Step 2: List conditions in priority order
```dax
[Total Sales] >= 10000, "Platinum",
[Total Sales] >= 5000, "Gold",
```
Order matters — the first matching condition wins, so check the highest/most specific threshold first.

### Step 3: Provide a default/fallback
```dax
"Bronze"
```
The final, unpaired value acts as the `ELSE` — always include one to avoid unexpected blanks.

---

## 🚀 Common Use Cases

- Sales tiers, performance ratings, customer segments
- Categorizing a numeric measure into labeled buckets for a slicer or axis
- Mapping a code or abbreviation to a friendly display label

---

## ⚖️ Alternatives

### Nested IF()
```dax
Sales Tier =
IF([Total Sales] >= 10000, "Platinum",
	IF([Total Sales] >= 5000, "Gold",
		IF([Total Sales] >= 1000, "Silver", "Bronze")
	)
)
```
Functionally equivalent, but harder to read and easier to make a bracket-matching mistake in once nesting goes more than 2-3 levels deep.

### A calculated/lookup table for very complex mappings
For a large number of categories or complex mapping logic, a separate mapping table joined via a relationship can be cleaner than an enormous `SWITCH()`.

---

## ⚠️ Performance Considerations

- `SWITCH()` and nested `IF()` have similar performance characteristics — the choice here is almost entirely about readability, not speed.
- A very long `SWITCH(TRUE(), ...)` chain evaluated per row (inside a calculated column) can add up on large tables — consider a mapping table for dozens of conditions.

---

## 🚨 Common Mistakes

- Ordering conditions incorrectly in a `SWITCH(TRUE(), ...)` chain — since the first match wins, a broad condition placed too early can shadow a more specific one below it.
- Forgetting the default/fallback value, leaving unmatched rows blank instead of a sensible label.
- Using `SWITCH([Column], ...)` (exact match mode) when range-based logic was actually needed — that requires the `SWITCH(TRUE(), ...)` form instead.

---

## 💡 Wisdom from Mímir

For threshold/bucket logic, always order conditions from most restrictive to least restrictive — highest tier first, working down to the catch-all default. Getting this order backward is the most common bug in this pattern, and it fails silently rather than erroring.

---

## 🔗 Related Notes

- [[DAX Fundamentals]]
- [[Bucketing Values Into Ranges With CASE]]
