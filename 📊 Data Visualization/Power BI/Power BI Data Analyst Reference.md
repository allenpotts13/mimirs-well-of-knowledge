---
publish: true
status: published
permalink: power-bi/power-bi-data-analyst-reference
description: "The 30 Power BI DAX functions every data analyst should know, with a one-line explanation beside each."
---

# <span class="rune">ᛊ</span> Power BI Data Analyst Reference

> Thirty DAX functions cover the overwhelming majority of everyday report-building work — filter context manipulation, time intelligence, and row-by-row aggregation. ★ marks the ones that come up constantly, especially `CALCULATE`, which is arguably the single most important function in the entire language. See the full [[Power BI Codex|Power BI Codex]] for depth.

---

## The 30 Functions Every Data Analyst Should Know

| # | Function | What It Does |
|---:|---|---|
| 1 | `SUM()` ★ | Adds all the numbers in a column |
| 2 | `COUNT()` | Counts the rows containing numbers in a column |
| 3 | `AVERAGE()` | Calculates the average of a column |
| 4 | `MIN()` / `MAX()` | Returns the smallest or largest value in a column |
| 5 | `CALCULATE()` ★ | Evaluates an expression inside a modified filter context — the foundation nearly every other DAX pattern is built on |
| 6 | `FILTER()` | Returns a table containing only the rows matching a given condition |
| 7 | `ALL()` | Removes filters from a table or column, ignoring whatever's currently selected |
| 8 | `ALLEXCEPT()` | Removes all filters except the ones explicitly specified |
| 9 | `VALUES()` | Returns the distinct values currently visible in a column |
| 10 | `DISTINCT()` | Returns a table of distinct values or rows |
| 11 | `RELATED()` | Pulls a value from a related table on the "one" side of a many-to-one relationship |
| 12 | `RELATEDTABLE()` | Pulls a related table of rows from the "many" side of a one-to-many relationship |
| 13 | `USERELATIONSHIP()` | Activates a normally-inactive relationship for use in one specific calculation |
| 14 | `TOTALYTD()` | Calculates a year-to-date running total |
| 15 | `DATESYTD()` | Returns a table of every date from the start of the year up to a given date |
| 16 | `SAMEPERIODLASTYEAR()` | Returns the same set of dates, shifted back exactly one year |
| 17 | `DATEADD()` | Shifts a column of dates forward or backward by a specified time interval |
| 18 | `DIVIDE()` ★ | Performs division while safely handling divide-by-zero with a specified fallback result |
| 19 | `SWITCH()` | Evaluates an expression against a list of possible values — a cleaner alternative to nested IFs |
| 20 | `IF()` | Returns one value if a condition is true, another if false |
| 21 | `HASONEVALUE()` | Checks whether a column currently has exactly one distinct value visible in the current context |
| 22 | `SELECTEDVALUE()` | Returns a column's single selected value, or a specified default if there isn't exactly one |
| 23 | `SUMX()` | Sums the result of an expression evaluated row by row across a table |
| 24 | `AVERAGEX()` | Averages the result of an expression evaluated row by row across a table |
| 25 | `COUNTROWS()` | Counts the number of rows in a table |
| 26 | `RANKX()` | Ranks a value against a set of values within a table |
| 27 | `TOPN()` | Returns the top N rows of a table, ordered by a given expression |
| 28 | `CONCATENATEX()` | Concatenates the result of an expression across multiple rows into a single text string |
| 29 | `ISBLANK()` | Checks whether a value is blank |
| 30 | `KEEPFILTERS()` | Preserves existing filters instead of overwriting them when used inside CALCULATE |

---

## 🚀 Where These Show Up Most

- **CALCULATE** — the function everything else in DAX ultimately builds on; understanding filter context is the real skill, and CALCULATE is where it's manipulated
- **Time intelligence** (`TOTALYTD`, `SAMEPERIODLASTYEAR`, `DATEADD`) — year-over-year comparisons and running totals, among the most requested report features in any business setting
- **Iterators** (`SUMX`, `AVERAGEX`, `RANKX`) — anything that needs a calculation performed row by row rather than on a whole column at once
- **DIVIDE over `/`** — the safe default for any division in a report, since a raw `/` throws a visible error the moment a denominator hits zero

---

## 🔗 Related Notes

- [[Power BI Codex]]
- [[Power BI Cheat Sheet]]
- [[Excel Data Analyst Reference]]
- [[SQL Data Analyst Reference]]
- [[Python Data Analyst Reference]]
