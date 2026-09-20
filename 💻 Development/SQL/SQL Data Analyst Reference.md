---
publish: true
status: published
permalink: sql/sql-data-analyst-reference
description: "The 30 SQL keywords and functions every data analyst should know, with a one-line explanation beside each."
---

# <span class="rune">ᛊ</span> SQL Data Analyst Reference

> Thirty keywords cover the overwhelming majority of everyday analyst querying — filtering, joining, aggregating, and window-ranking. ★ marks the one every query starts with. See the full [[SQL Codex|SQL Codex]] for depth on any of these.

---

## The 30 Keywords Every Data Analyst Should Know

| # | Keyword | What It Does |
|---:|---|---|
| 1 | `SELECT` ★ | Retrieves specific columns from a table |
| 2 | `DISTINCT` | Removes duplicate rows from the result |
| 3 | `FROM` | Specifies the source table(s) a query reads from |
| 4 | `WHERE` | Filters individual rows, before any grouping happens |
| 5 | `GROUP BY` | Collapses rows sharing a value into summary groups for aggregation |
| 6 | `ORDER BY` | Sorts the final result set |
| 7 | `HAVING` | Filters *groups* after aggregation — the WHERE clause for GROUP BY results |
| 8 | `JOIN` | Combines rows from two or more tables based on a related column |
| 9 | `LEFT JOIN` | Returns all rows from the left table, plus matches from the right (NULL where none exist) |
| 10 | `RIGHT JOIN` | Returns all rows from the right table, plus matches from the left (NULL where none exist) |
| 11 | `INNER JOIN` | Returns only the rows with a match in both tables |
| 12 | `FULL JOIN` | Returns all rows from both tables, matched wherever possible |
| 13 | `CROSS JOIN` | Returns every possible combination of rows from both tables (a Cartesian product) |
| 14 | `UNION` | Combines the results of multiple queries into one, removing duplicates |
| 15 | `UNION ALL` | Combines the results of multiple queries, keeping duplicates (faster than UNION) |
| 16 | `CASE WHEN` | Adds conditional, if/then logic directly inside a query |
| 17 | `COALESCE` | Returns the first non-NULL value from a list of options |
| 18 | `COUNT()` | Counts rows, or non-NULL values in a specific column |
| 19 | `SUM()` | Totals a numeric column |
| 20 | `AVG()` | Calculates the average of a numeric column |
| 21 | `MIN()` | Returns the smallest value in a column |
| 22 | `MAX()` | Returns the largest value in a column |
| 23 | `BETWEEN` | Filters values within an inclusive range |
| 24 | `EXISTS` | Tests whether a subquery returns any rows at all |
| 25 | `IN` | Matches a value against a list of acceptable possibilities |
| 26 | `CTE (WITH)` | Defines a named, temporary result set that can be referenced elsewhere in the same query |
| 27 | `ROW_NUMBER()` | Assigns a unique, sequential number to each row within a partition |
| 28 | `RANK()` | Assigns a rank to rows, leaving a gap in the sequence after ties |
| 29 | `DENSE_RANK()` | Assigns a rank to rows, with no gap in the sequence after ties |
| 30 | `LIMIT` | Restricts how many rows a query returns |

---

## 🚀 Where These Show Up Most

- **Filtering vs. group filtering** (`WHERE` vs. `HAVING`) — the single most common beginner mix-up, and the fastest way to spot a query that will run slower than it needs to
- **Joins** (`INNER`, `LEFT`, `RIGHT`, `FULL`) — combining a fact table with lookup/dimension tables, the backbone of nearly every analytical query
- **Window functions** (`ROW_NUMBER`, `RANK`, `DENSE_RANK`) — finding the top N per group, deduplicating, or ranking without collapsing the underlying rows
- **CTEs** — breaking a complex multi-step query into readable, named stages instead of deeply nested subqueries

---

## 🔗 Related Notes

- [[SQL Codex]]
- [[SQL Cheat Sheet]]
- [[Excel Data Analyst Reference]]
- [[Power BI Data Analyst Reference]]
- [[Python Data Analyst Reference]]
