---
type: concept
status: active
created: 2026-09-10
updated: 2026-09-10
technology: SQL
difficulty: Beginner
tags:
  - aggregation
  - query-structure
aliases:
  - GROUP BY
  - HAVING
---

# 📚 GROUP BY and HAVING

> GROUP BY collapses rows into per-group summaries; HAVING filters those groups after they're calculated.

---

## 🎯 Purpose

`GROUP BY` answers "one row per category" questions (total sales per region, count per department). `HAVING` then filters those *group-level* results — something `WHERE` can't do, since `WHERE` runs before grouping happens.

---

## 🧠 Key Ideas

- Every non-aggregated column in `SELECT` must appear in `GROUP BY`.
- Aggregate functions (`COUNT`, `SUM`, `AVG`, `MIN`, `MAX`) collapse many rows into one value per group.
- `WHERE` filters individual rows *before* grouping; `HAVING` filters groups *after* aggregation.
- Unlike [[Window Functions]], `GROUP BY` reduces row count — one output row per group, not one per input row.

---

## ⚙️ How It Works

```text
1. WHERE filters raw rows
2. GROUP BY collapses the remaining rows into groups
3. Aggregate functions compute one value per group
4. HAVING filters the resulting groups
```

Trying to filter on an aggregate in `WHERE` fails, because the aggregate doesn't exist yet at that stage — that's exactly what `HAVING` is for.

---

## 💻 Examples

```sql
SELECT
	Department,
	COUNT(*) AS EmployeeCount,
	AVG(Salary) AS AvgSalary
FROM Employees
WHERE HireDate >= '2020-01-01'   -- filters rows first
GROUP BY Department
HAVING COUNT(*) > 5;              -- filters groups after aggregation
```

This returns departments hired into since 2020 with more than 5 employees — see [[Finding Groups Below a Size Threshold]] for the inverse pattern.

---

## 🚀 Real World Applications

- Sales totals per region, per month, per product
- Departments above/below a headcount threshold
- Finding categories with unusually high or low averages

---

## ⚖️ Advantages

- The standard, most portable way to produce per-group summaries.
- `HAVING` makes group-level filtering explicit and readable.

---

## ⚠️ Limitations

- Collapses row-level detail — if individual rows are still needed alongside the aggregate, a [[Window Functions|window function]] fits better.
- Every unaggregated selected column must appear in `GROUP BY`, which some engines enforce strictly and others don't (silently returning arbitrary values if not).

---

## 🚨 Common Mistakes

- Trying to filter on an aggregate using `WHERE` instead of `HAVING` — this causes a syntax/semantic error, since the aggregate isn't computed yet at the `WHERE` stage.
- Selecting a column that isn't in `GROUP BY` and isn't aggregated, which some databases allow but return unpredictable results for.
- Using `HAVING` for a row-level filter that belongs in `WHERE` — this wastes work, filtering after aggregating instead of before.

---

## 📖 Further Reading

Documentation

Books

Videos

Articles

---

## 💡 Wisdom from Mímir

If the filter mentions an aggregate function (`COUNT`, `SUM`, `AVG`...), it belongs in `HAVING`. If it doesn't, it belongs in `WHERE` — putting a row-level filter in `HAVING` just delays it until after unnecessary aggregation work is already done.

---

## 🔗 Related Notes

- [[Window Functions]]
- [[Finding Groups Below a Size Threshold]]
- [[Counting Employees in Each Department]]
