---
type: dax-pattern
status: published
created: 2026-09-10
updated: 2026-09-10
technology: Power BI
difficulty: Intermediate
tags:
  - dax
  - time-intelligence
aliases:
  - YoY Growth
publish: true
---

# 🧩 Calculating Year-over-Year Growth

> Compare this year's number to last year's same period, and express the difference as a percentage.

---

## 🎯 Problem

Show how much revenue (or any measure) has grown or shrunk compared to the same period last year — one of the most requested KPIs in any business dashboard.

---

## 🤔 Mental Model

```text
Current period total ─┐
                       ├─→ difference ─→ % change
Prior year total ──────┘
```

Two numbers, compared, expressed as a ratio.

---

## 🧠 Why This Pattern Works

`SAMEPERIODLASTYEAR()` shifts the current date filter back exactly one year, letting the same base measure be re-evaluated against last year's matching period. `DIVIDE()` then safely computes the percentage change without risking a division-by-zero error.

---

## 💻 DAX Solution

```dax
Prior Year Sales =
CALCULATE(
	[Total Sales],
	SAMEPERIODLASTYEAR(DimDate[Date])
)

YoY Growth % =
DIVIDE(
	[Total Sales] - [Prior Year Sales],
	[Prior Year Sales]
)
```

---

## 🔄 Step-by-Step Breakdown

### Step 1: Calculate the prior year's equivalent total
```dax
CALCULATE([Total Sales], SAMEPERIODLASTYEAR(DimDate[Date]))
```
Shifts the current filter context back one year, then re-evaluates the same base measure.

### Step 2: Compute the difference
```dax
[Total Sales] - [Prior Year Sales]
```

### Step 3: Express it as a percentage, safely
```dax
DIVIDE(difference, [Prior Year Sales])
```
`DIVIDE()` returns a blank instead of an error when the prior year total is zero — see [[Handling Blank Values with DAX]].

---

## 🚀 Common Use Cases

- YoY revenue growth, YoY headcount change, YoY customer count change
- Any "compared to last year" KPI card or trend line

---

## ⚖️ Alternatives

### `DATEADD()` — more flexible period shifting
```dax
Prior Year Sales =
CALCULATE([Total Sales], DATEADD(DimDate[Date], -1, YEAR))
```
Behaves similarly to `SAMEPERIODLASTYEAR` for most calendars, but `DATEADD` also supports shifting by month or quarter, making it more general-purpose.

### Manual division without `DIVIDE()`
```dax
([Total Sales] - [Prior Year Sales]) / [Prior Year Sales]
```
Works, but throws an error (instead of a clean blank) when the prior year total is zero — `DIVIDE()` is the safer default.

---

## ⚠️ Performance Considerations

- Same date-table dependency as any time intelligence function — see [[Building a Date Table]].
- Computing two full measure evaluations (current + prior year) per cell roughly doubles the calculation cost versus a single measure — generally fine, but worth knowing on very large models.

---

## 🚨 Common Mistakes

- Using raw division (`/`) instead of `DIVIDE()`, causing a visible error whenever the prior year total is zero or blank.
- Applying `SAMEPERIODLASTYEAR` against a date column not tied to the model's marked date table.
- Forgetting the calculation needs a full prior year of data present in the model — if the source data doesn't go back that far, the prior year total is simply blank, not wrong.

---

## 💡 Wisdom from Mímir

Always use `DIVIDE()` for any DAX ratio, without exception. The difference between a blank cell and a big red error message, the first time a new product line has no prior-year data, is entirely down to that one habit.

---

## 🔗 Related Notes

- [[Time Intelligence in DAX]]
- [[Calculating Year-to-Date Totals]]
- [[Handling Blank Values with DAX]]
