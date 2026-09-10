---
type: query-pattern
status: published
created: 2026-09-02
updated: 2026-09-02
technology: SQL
difficulty: Intermediate
tags:
  - aggregate-functions
  - subqueries
  - grouping
  - reporting
  - arithmetic
aliases:
  - Percentage of Total by Group
  - Department Percentage Breakdown
publish: true
---

# 🧩 Calculating Each Group's Percentage of the Total

> *Divide each group's count (or sum) by the grand total, multiplied by 100, to express each group as a share of the whole.*

---

## 🎯 Problem

Calculate the percentage of employees in each department.

More generally, this pattern answers:
> What share of the whole does each group represent?

---

## 🤔 Mental Model

Imagine slicing a pie where the whole pie represents every employee in the company. Each department gets a slice sized in proportion to how many employees it has. To know how big a slice looks compared to the *entire* pie, you need two numbers at once: the size of that one slice, and the size of the whole pie — then you divide one by the other.

That's exactly what this pattern does: a per-group count divided by a company-wide total.

---

## 🧠 Why This Pattern Works

The outer query groups employees by `department_id` and counts them — that's the "slice." The subquery `(SELECT COUNT(*) FROM employees)` runs independently of the grouping and returns a single, fixed number — the total employee count across the whole table — that's the "whole pie." Dividing the slice by the pie and multiplying by 100 converts the ratio into a human-readable percentage.

The `* 100.0` is not decoration — it's essential. In many databases, dividing two integers (`COUNT(*) * 100`, then dividing by another integer) performs **integer division**, which truncates any fractional part. Multiplying by the decimal literal `100.0` instead of the integer `100` forces the arithmetic into floating-point/decimal territory *before* the division happens, so the result correctly preserves fractional percentages like `23.7` instead of truncating to `23` or, in the worst case, silently rounding all small percentages down to `0`.

---

## 💻 SQL Solution

```sql
SELECT department_id,
  (COUNT(*) * 100.0 / (SELECT COUNT(*) FROM employees)) AS percentage
FROM employees
GROUP BY department_id;
```

Writing `100.0` instead of `100` is a small, deliberate choice that prevents integer-division truncation.

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Group employees by department

```sql
FROM employees
GROUP BY department_id
```

Collapses employees into one row per department.

### Step 2 — Count employees per department

```sql
COUNT(*)
```

Gives the size of each department's "slice."

### Step 3 — Compute the grand total independently

```sql
(SELECT COUNT(*) FROM employees)
```

A subquery, unaffected by the outer `GROUP BY`, that returns the total employee count for the entire table — the "whole pie."

### Step 4 — Divide and scale to a percentage

```sql
COUNT(*) * 100.0 / (SELECT COUNT(*) FROM employees)
```

Multiplying by `100.0` first forces decimal arithmetic, then the division produces an accurate percentage rather than a truncated integer.

---

## 🧪 Example Data

| employee | department_id |
| -------- | -------------: |
| Alice    |             10 |
| Bob      |             10 |
| Carol    |             10 |
| Dave     |             20 |
| Eve      |             30 |
| Frank    |             30 |

Total employees: 6

Query:
```sql
SELECT department_id,
  (COUNT(*) * 100.0 / (SELECT COUNT(*) FROM employees)) AS percentage
FROM employees
GROUP BY department_id;
```

Result:

| department_id | percentage |
| -------------: | ---------: |
|             10 |      50.00 |
|             20 |      16.67 |
|             30 |      33.33 |

---

## 🚀 Common Use Cases

- Department headcount as a percentage of total workforce
- Market share by product category
- Vote share by candidate in an election tally
- Revenue contribution by region as a percentage of total revenue
- Error rate by error type as a percentage of all errors
- Traffic source breakdown as a percentage of total site visits

---

## ⚖️ Alternatives

### Window function (single pass, no subquery)

```sql
SELECT department_id,
  COUNT(*) * 100.0 / SUM(COUNT(*)) OVER () AS percentage
FROM employees
GROUP BY department_id;
```

`SUM(COUNT(*)) OVER ()` computes the grand total across all groups in the same query, without a separate subquery hitting the table again — often more efficient since the table is only scanned once.

### Percentage of a SUM rather than a COUNT

```sql
SELECT department_id,
  (SUM(salary) * 100.0 / (SELECT SUM(salary) FROM employees)) AS salary_share
FROM employees
GROUP BY department_id;
```

The same shape applies to any aggregate, not just `COUNT()` — here it expresses each department's share of total payroll rather than total headcount.

### CTE for readability

```sql
WITH totals AS (
  SELECT COUNT(*) AS total_employees FROM employees
)
SELECT e.department_id,
  COUNT(*) * 100.0 / t.total_employees AS percentage
FROM employees e
CROSS JOIN totals t
GROUP BY e.department_id, t.total_employees;
```

Computing the grand total once in a CTE and joining it in can be easier to read than a repeated inline subquery, especially if the total is needed in multiple places in a larger query.

---

## ⚠️ Performance Considerations

- The subquery form scans (or counts from an index on) the `employees` table twice — once for the grouped counts, once for the grand total. The window function alternative scans it only once.
- `COUNT(*)` on a large table benefits from any covering index, but a full grand total still typically requires examining the whole table (or an index that covers it) unless the count is cached elsewhere.
- Percentages calculated as `DECIMAL`/`FLOAT` carry rounding error; if percentages must sum to exactly 100% for display purposes, round consistently and reconcile the largest group last, or accept minor floating-point drift.
- For dashboards recalculating this frequently on a large table, consider caching the grand total rather than recomputing it on every request.

---

## 🚨 Common Mistakes

- **Using `* 100` instead of `* 100.0`** — in a database with integer division semantics, this silently truncates fractional percentages to whole numbers, or worse, rounds small percentages all the way down to `0`. This is the single most common bug in this pattern.
- Forgetting the grand-total subquery entirely and dividing by `COUNT(*)` from the grouped query itself, which just produces `100%` for every group (dividing a number by itself).
- Not handling division by zero when the table is empty — `(SELECT COUNT(*) FROM employees)` returning `0` turns the whole expression into a divide-by-zero error or `NULL`, depending on the database.
- Mixing up `COUNT(*)` (counts all rows, including `NULL`s in other columns) with `COUNT(column)` (ignores `NULL`s in that specific column) when computing either the slice or the whole pie — inconsistency here skews every percentage.
- Assuming percentages will sum to exactly 100.00 after rounding each row independently — rounding error can make totals sum to 99.99 or 100.01.

---

## 💡 Wisdom from Mímir

A percentage is a relationship, not a fact — it only means something in reference to the whole it was carved from.

> Before trusting any percentage, ask: **"100% of what, exactly?"**

The most dangerous version of this query is the one that computes a correct-looking number using the wrong denominator — a department can look small next to a company-wide total, and enormous next to a filtered subset. The arithmetic never lies; the choice of denominator can.

---

## 🔗 Related Notes

- [[Calculating a Total with SUM()]]
- [[Counting Employees in Each Department]]
- [[Bucketing Values Into Ranges With CASE]]
- [[Finding the Group With the Highest Count]]
- [[Calculating Multiple Aggregates Per Group]]
