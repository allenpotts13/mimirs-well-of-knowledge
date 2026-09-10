---
type: query-pattern
status: published
created: 2026-09-02
updated: 2026-09-02
technology: SQL
difficulty: Intermediate
tags:
  - window-functions
  - dense-rank
  - partition-by
  - top-n
  - grouping
aliases:
  - Second Highest Salary Per Department
  - Top N Per Group
  - DENSE_RANK with PARTITION BY
publish: true
permalink: sql/ranking-the-top-n-rows-within-each-group
---

# <span class="rune">ᚲ</span> Ranking the Top N Rows Within Each Group

> *Use `DENSE_RANK() OVER (PARTITION BY ... ORDER BY ...)` to rank rows independently inside each group, then filter on the rank.*

---

## 🎯 Problem

Find the second highest salary for each department.

More generally, this pattern answers:
> How do I find the top N rows *within each group* of a table, rather than the top N rows overall?

---

## 🤔 Mental Model

Picture splitting one long leaderboard into several smaller leaderboards — one per department — and re-numbering each one from 1st place downward, independently. `PARTITION BY` is what does the splitting; `ORDER BY` inside the window is what does the re-numbering within each split. Once every row has a rank *relative to its own group*, picking "the top N per group" is just filtering on that rank number.

---

## 🧠 Why This Pattern Works

A window function like `DENSE_RANK()` doesn't collapse rows the way `GROUP BY` does — every original row survives, but each one gains an extra computed column: its rank within its partition. `PARTITION BY department_id` tells the window function to reset its ranking counter every time the department changes, and `ORDER BY salary DESC` tells it which direction "top" means.

Because the rank is just another column, you can't filter on it directly in the same `SELECT` where it's computed — `WHERE` runs before window functions are evaluated. That's why this pattern always wraps the ranked query in a subquery (or CTE) and applies the `WHERE rnk = 2` (or `<= N`) filter one level up, against the already-materialized rank column.

---

## 💻 SQL Solution

```sql
SELECT department_id, salary
FROM (
    SELECT
        department_id,
        salary,
        DENSE_RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) AS rnk
    FROM employees
) AS ranked_salaries
WHERE rnk = 2;
```

Aliasing the rank column as `rnk` (rather than `rank`, a reserved word in modern MySQL) avoids a naming collision with the SQL keyword.

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Partition the rows by group

```sql
PARTITION BY department_id
```

MySQL conceptually splits the `employees` table into separate buckets, one per distinct `department_id`.

### Step 2 — Order each partition independently

```sql
ORDER BY salary DESC
```

Within each department's bucket, rows are sorted from highest to lowest salary — this ordering only affects ranking within that bucket, not across the whole table.

### Step 3 — Assign a dense rank per partition

```sql
DENSE_RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) AS rnk
```

Every row gets a rank starting at 1 within its own department, restarting for each new department, with tied salaries receiving the same rank and no gaps afterward.

### Step 4 — Materialize the ranked result as a subquery

```sql
FROM (SELECT ... ) AS ranked_salaries
```

Because `WHERE` cannot filter on a window function's output in the same query level where it's defined, the ranked rows are wrapped in a derived table first.

### Step 5 — Filter on the rank

```sql
WHERE rnk = 2
```

Only rows whose computed rank equals 2 survive — the second-highest salary in each department.

---

## 🧪 Example Data

| department_id | employee | salary |
| ------------- | -------- | -----: |
| 10            | Alice    |  90000 |
| 10            | Bob      |  85000 |
| 10            | Carol    |  85000 |
| 10            | Dan      |  70000 |
| 20            | Erin     |  95000 |
| 20            | Frank    |  60000 |

After applying `DENSE_RANK() OVER (PARTITION BY department_id ORDER BY salary DESC)`:

| department_id | employee | salary | rnk |
| ------------- | -------- | -----: | --- |
| 10            | Alice    |  90000 | 1   |
| 10            | Bob      |  85000 | 2   |
| 10            | Carol    |  85000 | 2   |
| 10            | Dan      |  70000 | 3   |
| 20            | Erin     |  95000 | 1   |
| 20            | Frank    |  60000 | 2   |

Query result (`WHERE rnk = 2`):

| department_id | salary |
| ------------- | -----: |
| 10            |  85000 |
| 20            |  60000 |

Note department 10 returns **two** rows (Bob and Carol) — both genuinely tied for second place, which is exactly what `DENSE_RANK()` is designed to preserve.

---

## 🚀 Common Use Cases

- Finding the Nth highest salary, score, or price within each category
- Selecting the top 3 best-selling products per store or region
- Identifying the most recent N orders per customer
- Surfacing the top-performing employee per team for a leaderboard
- Picking the latest N log entries per user session
- Building "runner-up" reports (2nd place per group) for awards or bonuses

---

## ⚖️ Alternatives

### Top N per group (N > 1)

```sql
SELECT *
FROM (
    SELECT *,
        DENSE_RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) AS rnk
    FROM employees
) AS ranked_employees
WHERE rnk <= 3;
```

The identical technique generalizes trivially from "the Nth row" to "the top N rows" simply by switching `= N` to `<= N`. This returns the three highest-paid employees in every department.

### Aggregating over the top N per group

```sql
SELECT department_id, AVG(salary) AS avg_top5_salary
FROM (
    SELECT
        department_id,
        salary,
        DENSE_RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) AS rnk
    FROM employees
) AS ranked_employees
WHERE rnk <= 5
GROUP BY department_id;
```

Once the top-N rows per group are isolated by the ranking subquery, they can be aggregated further — here, averaging the salaries of just the top 5 earners in each department, rather than the whole department.

### RANK() instead of DENSE_RANK()

```sql
SELECT department_id, salary
FROM (
    SELECT department_id, salary,
        RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) AS rnk
    FROM employees
) AS ranked_salaries
WHERE rnk = 2;
```

`RANK()` and `DENSE_RANK()` behave identically until there's a tie. `RANK()` leaves a **gap** after ties — two employees tied for 1st push the next rank to 3, skipping 2 entirely — which means `WHERE rnk = 2` could return **zero rows** even though there clearly is a "second highest" value in the business sense. `DENSE_RANK()` never skips, so it's almost always the correct choice for "Nth highest value" questions.

### ROW_NUMBER() instead of DENSE_RANK()

```sql
SELECT department_id, salary
FROM (
    SELECT department_id, salary,
        ROW_NUMBER() OVER (PARTITION BY department_id ORDER BY salary DESC) AS rnk
    FROM employees
) AS ranked_salaries
WHERE rnk = 2;
```

`ROW_NUMBER()` never repeats a number, even for exact ties — it arbitrarily picks one tied row to be "2nd" and the other to be "3rd." This guarantees exactly one row per group, which is useful when you need strictly one result per partition (e.g., "pick any one representative row"), but it silently hides the fact that a tie existed at all.

---

## ⚠️ Performance Considerations

- Window functions require MySQL to sort each partition's rows, so an index that already provides that order — e.g., a composite index on `(department_id, salary DESC)` — can meaningfully speed up the ranking step.
- The outer `WHERE rnk = N` filter cannot use an index directly, since `rnk` is a computed value that only exists after the window function runs; MySQL must materialize the ranked set before filtering it.
- On very large tables, computing ranks for every row just to discard all but a handful per group is more expensive than a targeted correlated subquery would be for a single group — but far simpler and more maintainable when you need the answer for *every* group at once.
- `EXPLAIN` the query on production-sized data before assuming any one ranking function is "faster" than another — the difference is usually in tie-handling correctness, not raw speed.

---

## 🚨 Common Mistakes

- Using `RANK()` when the intent is `DENSE_RANK()` — ties create gaps, and `WHERE rnk = 2` can return nothing even though a legitimate second-highest value exists.
- Using `ROW_NUMBER()` when ties should be preserved — it silently drops one of the tied rows instead of returning both.
- Attempting to filter on the window function directly in the same `SELECT`/`WHERE` level where it's defined (`WHERE DENSE_RANK() OVER (...) = 2` is not valid) instead of wrapping it in a subquery first.
- Forgetting `PARTITION BY` entirely and ranking across the whole table, which answers "top N overall" rather than "top N per group."
- Mismatching the `ORDER BY` direction — using `ASC` when the goal is "highest" returns the *lowest* values instead.

---

## 💡 Wisdom from Mímir

Every "top N per group" question is secretly two questions stacked on top of each other:
> "How do I define an ordering *within* a group?" and then, separately, **"How many rows from that ordering do I actually want?"**

`PARTITION BY` answers the first question; the outer `WHERE` clause answers the second. Keep them separate in your head and the pattern never gets confusing, no matter how the number N changes.

---

## 🔗 Related Notes

- [[Finding the Second Highest Value]]
- [[Finding the Nth Highest Value]]
- [[Retrieving the Top N Highest Values]]
- [[Finding the Highest Salary in Each Department]]
- [[Finding the Group With the Highest Count]]
- [[Calculating Multiple Aggregates Per Group]]
