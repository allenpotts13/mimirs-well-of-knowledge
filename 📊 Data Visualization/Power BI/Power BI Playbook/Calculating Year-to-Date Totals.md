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
  - YTD Measure
publish: true
permalink: power-bi/calculating-year-to-date-totals
---

# <span class="rune">ᚲ</span> Calculating Year-to-Date Totals

> Sum everything from the start of the current year up to the latest date in view — without hand-writing date range math.

---

## 🎯 Problem

Show a running total from January 1st of the current year through whatever the latest filtered date is — a standard executive dashboard KPI.

---

## 🤔 Mental Model

```text
Filtered to March 2026 in a visual
         ↓
YTD expands that filter backward to Jan 1, 2026 → March 2026
         ↓
[Total Sales] evaluates against that expanded date range instead
```

---

## 🧠 Why This Pattern Works

`DATESYTD()` returns a table of dates spanning from the start of the current year to the last date in the current filter context. Wrapping the base measure in `CALCULATE()` with that date table as the filter re-evaluates the measure against the expanded range — see [[The CALCULATE Function]] and [[Time Intelligence in DAX]].

---

## 💻 DAX Solution

```dax
YTD Sales =
CALCULATE(
	[Total Sales],
	DATESYTD(DimDate[Date])
)
```

---

## 🔄 Step-by-Step Breakdown

### Step 1: Start from an existing base measure
```dax
[Total Sales]
```
YTD is a modification of an existing total, not a new calculation from scratch.

### Step 2: Wrap it in CALCULATE with DATESYTD
```dax
CALCULATE([Total Sales], DATESYTD(DimDate[Date]))
```
`DATESYTD` expands the date filter to the full year-to-date range.

### Step 3: Reference the marked date table's date column
```dax
DimDate[Date]
```
Must come from a table marked as the model's official Date table — see [[Building a Date Table]].

---

## 🚀 Common Use Cases

- Year-to-date revenue, YTD units sold, YTD expenses on an executive dashboard
- Comparing YTD this year vs. YTD same point last year (pair with [[Calculating Year-over-Year Growth]])

---

## ⚖️ Alternatives

### `TOTALYTD()` — a shorthand wrapper
```dax
YTD Sales = TOTALYTD(SUM(Sales[SalesAmount]), DimDate[Date])
```
Functionally similar, slightly more compact syntax for simple cases.

### Custom fiscal year-end
```dax
DATESYTD(DimDate[Date], "06/30")
```
Needed when the fiscal year doesn't end December 31st.

---

## ⚠️ Performance Considerations

- Depends entirely on a clean, continuous, correctly marked date table — gaps in the date range silently break the calculation.
- Generally cheap to evaluate, but stacking many time intelligence measures on top of each other in a large matrix can add up.

---

## 🚨 Common Mistakes

- Using a date column that isn't part of the model's marked Date table.
- A date table with gaps (missing dates) or that doesn't extend far enough back/forward for the data.
- Forgetting the fiscal year argument for organizations that don't run a calendar-year fiscal calendar.

---

## 💡 Wisdom from Mímir

If YTD numbers look wrong, check the date table before the DAX — see [[Building a Date Table]]. A broken or incomplete date table is the root cause behind the overwhelming majority of "my time intelligence isn't working" problems.

---

## 🔗 Related Notes

- [[Time Intelligence in DAX]]
- [[Building a Date Table]]
- [[Calculating Year-over-Year Growth]]
- [[The CALCULATE Function]]
