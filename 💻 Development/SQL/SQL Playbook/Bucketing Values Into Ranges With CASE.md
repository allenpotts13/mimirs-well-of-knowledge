---
type: query-pattern
status: active
created: 2026-09-02
updated: 2026-09-02
technology: SQL
difficulty: Intermediate
tags:
  - case-expressions
  - grouping
  - aggregate-functions
  - reporting
aliases:
  - Age Bracket Grouping
  - Salary Range Buckets
  - CASE WHEN Binning
---

# 🧩 Bucketing Values Into Ranges With CASE

> *Use a `CASE` expression to turn a continuous numeric column into named categories, then `GROUP BY` those categories.*

---

## 🎯 Problem

Display employees grouped by their age brackets (e.g., 20-30, 31-40, etc.).

More generally, this pattern answers:
> How do I turn a continuous number into a category, and then count or summarize by that category?

---

## 🤔 Mental Model

Think of sorting mail into pigeonholes at a post office. Each piece of mail (each row) has a specific, exact value — but you don't want a separate pigeonhole for every possible value. Instead, you define a handful of labeled slots ("20-30," "31-40," "41+") and drop each piece of mail into whichever slot it belongs in. `CASE` is the hand doing the sorting; `GROUP BY` is what lets you then count how full each slot ended up.

---

## 🧠 Why This Pattern Works

A `CASE` expression inside a `SELECT` list behaves like an inline, row-by-row decision tree: for every row, it evaluates each `WHEN` condition in order and returns the first matching label. Because it returns an ordinary value — just like any column — that computed label can be aliased, selected, and, crucially, grouped on exactly like a real column.

This is the trick that makes bucketing possible: `GROUP BY` doesn't require a value that's *stored* in the table, only a value that's *produced* by the query. `CASE` manufactures a brand-new categorical column on the fly from a continuous one (age, salary, price, etc.), and `GROUP BY` then aggregates rows by that manufactured category rather than by the raw underlying number.

---

## 💻 SQL Solution

```sql
SELECT
  CASE
    WHEN age BETWEEN 20 AND 30 THEN '20-30'
    WHEN age BETWEEN 31 AND 40 THEN '31-40'
    ELSE '41+'
  END AS age_bracket,
  COUNT(*) AS employee_count
FROM employees
GROUP BY age_bracket;
```

Ordering the `WHEN` clauses from lowest range to highest keeps the bracket boundaries easy to read and reason about.

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Define the buckets with WHEN conditions

```sql
CASE
  WHEN age BETWEEN 20 AND 30 THEN '20-30'
  WHEN age BETWEEN 31 AND 40 THEN '31-40'
  ELSE '41+'
END
```

Each `WHEN` checks a range; the first one that matches wins. `ELSE` catches everything not explicitly covered.

### Step 2 — Alias the computed bucket

```sql
AS age_bracket
```

Gives the new categorical value a name so it can be referenced later in the query.

### Step 3 — Count rows per bucket

```sql
COUNT(*) AS employee_count
```

Counts how many employees fell into each bracket.

### Step 4 — Group by the computed bucket

```sql
GROUP BY age_bracket
```

Collapses all rows sharing the same bracket label into a single summary row.

---

## 🧪 Example Data

| employee | age |
| -------- | --: |
| Alice    |  24 |
| Bob      |  29 |
| Carol    |  35 |
| Dave     |  38 |
| Eve      |  47 |

Query:
```sql
SELECT
  CASE
    WHEN age BETWEEN 20 AND 30 THEN '20-30'
    WHEN age BETWEEN 31 AND 40 THEN '31-40'
    ELSE '41+'
  END AS age_bracket,
  COUNT(*) AS employee_count
FROM employees
GROUP BY age_bracket;
```

Result:

| age_bracket | employee_count |
| ----------- | --------------: |
| 20-30       |               2 |
| 31-40       |               2 |
| 41+         |               1 |

---

## 🚀 Common Use Cases

- Age demographics reporting (20-30, 31-40, 41+)
- Salary bands for compensation analysis
- Price tiers for product catalogs (budget, mid-range, premium)
- Grading scales (A/B/C/D/F from numeric scores)
- Response-time buckets for performance dashboards (fast, medium, slow)
- Order-size segmentation for customer analytics (small, medium, large)

---

## ⚖️ Alternatives

### Salary range bucketing (same technique, different column)

```sql
SELECT
  CASE
    WHEN salary BETWEEN 0 AND 20000 THEN '0-20K'
    WHEN salary BETWEEN 20001 AND 50000 THEN '20K-50K'
    ELSE '50K+'
  END AS salary_range,
  COUNT(*) AS employee_count
FROM employees
GROUP BY salary_range;
```

Identical structure applied to a different continuous column — proof that this is a general bucketing pattern, not something specific to ages.

### NTILE() for equal-sized buckets

```sql
SELECT *, NTILE(4) OVER (ORDER BY age) AS age_quartile
FROM employees;
```

Instead of defining bucket *boundaries* yourself, `NTILE(4)` splits the rows into 4 roughly equal-sized groups based on rank. Useful when you want balanced group sizes rather than fixed, meaningful ranges.

### Bucketing with FLOOR() for evenly-sized numeric bins

```sql
SELECT FLOOR(age / 10) * 10 AS decade_bucket, COUNT(*) AS employee_count
FROM employees
GROUP BY decade_bucket;
```

Useful for simple, evenly-spaced bins (e.g., every 10 years) without writing out each `WHEN` clause by hand — though it's less readable when bucket boundaries aren't uniform.

---

## ⚠️ Performance Considerations

- `CASE` expressions are evaluated per row and are generally inexpensive, but grouping on a computed expression prevents the database from using a simple index seek on the raw column the way it could for `GROUP BY age`.
- For very large tables, consider adding a real, indexed "bucket" column populated at write time if this grouping is queried frequently, rather than recomputing `CASE` on every read.
- Make sure `WHEN` ranges don't overlap and don't leave gaps — overlapping ranges silently bucket rows into whichever condition is listed first, and gaps silently fall through to `ELSE`.
- `BETWEEN` is inclusive on both ends — verify adjacent ranges don't double-count a boundary value (e.g., age 30 and 31 must not both be reachable by the same `WHEN`, nor should either be missed).

---

## 🚨 Common Mistakes

- Grouping by the full `CASE` expression's alias, e.g., `GROUP BY age_bracket` — this works in MySQL, which allows grouping by a `SELECT`-list alias, but it is **not standard SQL** and does not work in every database. Some engines (and MySQL in `ONLY_FULL_GROUP_BY` strict configurations) require repeating the entire `CASE` expression in the `GROUP BY` clause instead of the alias.
- Leaving gaps between `WHEN` ranges, so certain values fall through to an unintended bucket (or an unhandled `NULL` result if there's no `ELSE`).
- Forgetting the `ELSE` clause entirely, which causes any unmatched row to return `NULL` for the bucket — and `NULL` becomes its own silent, unlabeled group after `GROUP BY`.
- Writing overlapping ranges (e.g., `20-30` and `30-40` both including 30), causing ambiguity about which bucket a boundary value lands in.
- Hardcoding bucket boundaries that don't match the business's actual definitions (e.g., an off-by-one on inclusive vs. exclusive ranges).

---

## 💡 Wisdom from Mímir

A continuous number tells you exactly where something sits — but exactness is often *too much* information for a human to act on. Nobody staffs a meeting around "the employee who is 34.2 years old"; they staff it around "employees in their 30s."

> A bucket is a deliberate act of forgetting precision in exchange for meaning.

Choose your boundaries as carefully as you choose your data — a bucket drawn in the wrong place answers a question nobody asked.

---

## 🔗 Related Notes

- [[Finding Values Within a Range]]
- [[Counting Employees in Each Department]]
- [[Updating Values Conditionally with CASE]]
- [[Finding Values in a Statistical Percentile]]
- [[Calculating Each Group's Percentage of the Total]]
