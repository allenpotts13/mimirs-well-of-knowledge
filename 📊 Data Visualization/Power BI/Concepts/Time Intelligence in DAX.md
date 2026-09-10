---
type: concept
status: published
created: 2026-09-10
updated: 2026-09-10
technology: Power BI
difficulty: Advanced
tags:
  - power-bi
  - dax
  - time-intelligence
aliases:
  - DATESYTD
  - SAMEPERIODLASTYEAR
publish: true
---

# 📚 Time Intelligence in DAX

> A family of built-in DAX functions that handle date-based comparisons — year-to-date, prior period, rolling averages — without hand-writing the date math.

---

## 🎯 Purpose

Nearly every business report needs to answer "how does this compare to last year" or "what's the running total this year." Time intelligence functions handle these comparisons correctly, as long as the model has a proper date table to work from.

---

## 🧠 Key Ideas

- Requires a dedicated **date table**, marked as an official Date table in the model — see [[Building a Date Table]].
- Most time intelligence functions are just `CALCULATE()` with a special date-filter argument built in.
- Common functions: `DATESYTD`, `DATESQTD`, `DATESMTD` (year/quarter/month-to-date), `SAMEPERIODLASTYEAR`, `PARALLELPERIOD`, `DATEADD`, `TOTALYTD`.
- Time intelligence functions need a continuous, unbroken date range in the date table — gaps break the calculation.

---

## ⚙️ How It Works

```dax
YTD Sales =
CALCULATE(
	[Total Sales],
	DATESYTD(DimDate[Date])
)
```

`DATESYTD()` returns a table of dates from the start of the current year up to the latest date in the current filter context. `CALCULATE` then evaluates `[Total Sales]` against that expanded date range instead of whatever single period was originally filtered.

---

## 💻 Examples

```dax
YTD Sales =
CALCULATE([Total Sales], DATESYTD(DimDate[Date]))

Prior Year Sales =
CALCULATE([Total Sales], SAMEPERIODLASTYEAR(DimDate[Date]))

YoY Growth % =
DIVIDE(
	[Total Sales] - [Prior Year Sales],
	[Prior Year Sales]
)

Rolling 3-Month Average =
AVERAGEX(
	DATESINPERIOD(DimDate[Date], MAX(DimDate[Date]), -3, MONTH),
	[Total Sales]
)
```

---

## 🚀 Real World Applications

- Year-over-year and month-over-month growth percentages
- YTD/QTD/MTD summary cards on an executive dashboard
- Rolling averages to smooth out noisy day-to-day data

---

## ⚖️ Advantages

- Handles date-range edge cases (leap years, partial periods, fiscal calendars with the right setup) automatically.
- Far less error-prone than hand-writing date-range filters with raw comparison operators.

---

## ⚠️ Limitations

- Completely dependent on a correctly marked, continuous date table — breaks or behaves oddly without one.
- Fiscal calendars that don't align to the standard Jan–Dec year need extra setup (a fiscal date table, or the optional year-end-date argument some functions accept).

---

## 🚨 Common Mistakes

- Using time intelligence functions without a marked date table — results become unreliable or the functions fail outright.
- Forgetting the date table needs every calendar date in its range represented, even dates with no sales — gaps break period calculations.
- Confusing `SAMEPERIODLASTYEAR` (same dates, prior year) with a naive `DATEADD(..., -1, YEAR)` — they're usually equivalent, but not always, depending on the calendar.

---

## 📖 Further Reading

Documentation

Books

Videos

Articles

---

## 💡 Wisdom from Mímir

Time intelligence problems are almost always date table problems in disguise. If YTD or prior-year numbers look wrong, check for gaps in the date table or a missing "Mark as Date Table" setting before suspecting the DAX itself.

---

## 🔗 Related Notes

- [[The CALCULATE Function]]
- [[Building a Date Table]]
- [[Calculating Year-to-Date Totals]]
- [[Calculating Year-over-Year Growth]]
