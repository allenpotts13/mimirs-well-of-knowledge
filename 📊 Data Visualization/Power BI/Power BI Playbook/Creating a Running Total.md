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
  - running-total
aliases:
  - Cumulative Total
publish: true
---

# 🧩 Creating a Running Total

> Accumulate a measure over time — each point on the chart shows everything up to and including that date, not just that date's own value.

---

## 🎯 Problem

Show a cumulative total (running revenue, running signups) that grows across a time axis, where each point includes everything before it.

---

## 🤔 Mental Model

```text
Jan:  100        → running total: 100
Feb:  150        → running total: 250
Mar:  200        → running total: 450
```

Each point's running total = every period's value up to and including that point.

---

## 🧠 Why This Pattern Works

`CALCULATE()` combined with a filter that includes "every date up to and including the current one" re-evaluates the base measure across that expanding window instead of just the current period. `FILTER(ALL(...))` builds that expanding date range explicitly.

---

## 💻 DAX Solution

```dax
Running Total Sales =
CALCULATE(
	[Total Sales],
	FILTER(
		ALL(DimDate[Date]),
		DimDate[Date] <= MAX(DimDate[Date])
	)
)
```

---

## 🔄 Step-by-Step Breakdown

### Step 1: Remove the existing date filter with ALL()
```dax
ALL(DimDate[Date])
```
Clears whatever single-period filter the visual applied, giving access to every date in the table.

### Step 2: Filter that full date range down to "up to today"
```dax
FILTER(ALL(DimDate[Date]), DimDate[Date] <= MAX(DimDate[Date]))
```
`MAX(DimDate[Date])` still respects the current context's latest visible date, even though `ALL()` removed the date filter — this is what makes the window expand correctly per row.

### Step 3: Evaluate the base measure against that expanded range
```dax
CALCULATE([Total Sales], <the filter above>)
```

---

## 🚀 Common Use Cases

- Running revenue or running signups on a line chart
- Cumulative progress toward a target (paired with a goal line)
- Any "total so far this year/quarter/month" visualization

---

## ⚖️ Alternatives

### `DATESYTD()` for a running total that resets each year
```dax
Running Total (Resets Yearly) = CALCULATE([Total Sales], DATESYTD(DimDate[Date]))
```
Simpler, but only accumulates within the current year rather than across the model's entire date range — see [[Calculating Year-to-Date Totals]].

### `TOTALYTD()` shorthand
Same idea as `DATESYTD`, more compact syntax, same yearly-reset limitation.

---

## ⚠️ Performance Considerations

- `FILTER(ALL(...))` scans the full date range for every point on the chart — on a very long date range with many data points, this can get noticeably slower than a simple `SUM()`.
- Restricting the running total to a specific table (rather than the whole date table) where possible reduces the scan size.

---

## 🚨 Common Mistakes

- Forgetting `ALL()`, which leaves the existing date filter in place and prevents the range from expanding at all — the "running" total ends up identical to the plain total.
- Using this pattern when a period-reset total (like YTD) was actually wanted — a true running total never resets, even across years.
- Applying it to a visual with a non-date axis, where "up to and including" doesn't have a clear ordering.

---

## 💡 Wisdom from Mímir

Before building a full unbounded running total, check whether the actual business need is really "resets every year" (use [[Calculating Year-to-Date Totals]] instead) — a true never-resetting running total is asked for far less often than it initially sounds.

---

## 🔗 Related Notes

- [[Time Intelligence in DAX]]
- [[Calculating Year-to-Date Totals]]
- [[The CALCULATE Function]]
