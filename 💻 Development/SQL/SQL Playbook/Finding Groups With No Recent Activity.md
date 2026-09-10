---
type: query-pattern
status: published
created: 2026-09-02
updated: 2026-09-02
technology: SQL
difficulty: Intermediate
tags:
  - subqueries
  - having
  - aggregate-functions
  - date-functions
  - staleness-detection
aliases:
  - Finding Inactive Groups
  - Departments With No Recent Hires
  - Detecting Stale Groups
publish: true
permalink: sql/finding-groups-with-no-recent-activity
---

# <span class="rune">ᚲ</span> Finding Groups With No Recent Activity

> *Use `MAX()` on a date column with `HAVING` to find groups whose most recent activity is older than a cutoff.*

---

## 🎯 Problem

List all employees in departments that have not hired anyone in the past 2 years.

More generally, this pattern answers:
> Which groups have gone quiet — where the most recent event in the group is older than some cutoff?

---

## 🤔 Mental Model

Picture each department as a household with a mailbox. Every hire is a piece of mail landing in the box, stamped with today's date. To find the households that have "gone dark," you don't need to read every letter — you only need to check the date on the most recent one. If even the newest letter in the box is old, the whole household has been inactive.

`MAX(hire_date)` is that "newest letter" check, done once per department.

---

## 🧠 Why This Pattern Works

The inner query groups employees by `department_id` and computes `MAX(hire_date)` for each group — the single most recent hire date in that department. `HAVING` then filters those group-level results, keeping only the departments whose most recent hire date is older than the cutoff (`CURDATE()` minus 2 years).

This works because "no recent activity" is really a statement about the *maximum* of a timestamp column, not about every row individually. If the newest event in a group already fails the recency test, every other event in that group necessarily fails it too — so checking the max is both correct and efficient. The outer query then uses that list of stale `department_id` values to pull back every employee who belongs to one of those departments.

---

## 💻 SQL Solution

```sql
SELECT * FROM employees
WHERE department_id IN (
  SELECT department_id FROM employees
  GROUP BY department_id
  HAVING MAX(hire_date) < ADDDATE(CURDATE(), INTERVAL -2 YEAR)
);
```

`ADDDATE(CURDATE(), INTERVAL -2 YEAR)` reads naturally as "today, minus 2 years" and is equivalent to `CURDATE() - INTERVAL 2 YEAR`.

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Group employees by department

```sql
SELECT department_id FROM employees
GROUP BY department_id
```

Collapses all employees into one row per department.

### Step 2 — Find each department's most recent hire

```sql
MAX(hire_date)
```

For every department, this returns the single latest `hire_date` among its employees.

### Step 3 — Filter groups, not rows, with HAVING

```sql
HAVING MAX(hire_date) < ADDDATE(CURDATE(), INTERVAL -2 YEAR)
```

Keeps only the departments whose most recent hire happened more than 2 years ago. `HAVING` is required here (not `WHERE`) because the condition depends on an aggregate result.

### Step 4 — Pull back the full employee rows

```sql
SELECT * FROM employees
WHERE department_id IN (...)
```

The outer query uses the stale department IDs to retrieve every employee in those departments — not just an aggregate summary.

---

## 🧪 Example Data

| employee | department_id | hire_date  |
| -------- | -------------: | ---------- |
| Alice    |             10 | 2020-03-01 |
| Bob      |             10 | 2021-06-15 |
| Carol    |             20 | 2025-11-01 |
| Dave     |             30 | 2019-01-10 |

Assume today's date is 2026-09-02.

Query:
```sql
SELECT * FROM employees
WHERE department_id IN (
  SELECT department_id FROM employees
  GROUP BY department_id
  HAVING MAX(hire_date) < ADDDATE(CURDATE(), INTERVAL -2 YEAR)
);
```

- Department 10's latest hire is 2021-06-15 → older than 2024-09-02 → stale.
- Department 20's latest hire is 2025-11-01 → newer than 2024-09-02 → active.
- Department 30's latest hire is 2019-01-10 → older than 2024-09-02 → stale.

Result: Alice, Bob, and Dave (departments 10 and 30).

---

## 🚀 Common Use Cases

- Departments that haven't hired in years (workforce planning)
- Customers who haven't placed an order recently (churn detection)
- Accounts that haven't logged in recently (dormant-user reports)
- Repositories with no recent commits (project health checks)
- Products with no recent sales (slow-moving inventory)
- Support tickets whose category has had no recent updates

---

## ⚖️ Alternatives

### LEFT JOIN with a NOT EXISTS-style check

```sql
SELECT DISTINCT e.*
FROM employees e
LEFT JOIN employees recent
  ON recent.department_id = e.department_id
  AND recent.hire_date >= ADDDATE(CURDATE(), INTERVAL -2 YEAR)
WHERE recent.department_id IS NULL;
```

Instead of aggregating, this checks whether *any* row exists that would disqualify the department, then keeps only departments where no such row was found. It can perform differently than the subquery version depending on indexes and table size.

### Window function version

```sql
SELECT * FROM (
  SELECT *, MAX(hire_date) OVER (PARTITION BY department_id) AS dept_last_hire
  FROM employees
) AS ranked
WHERE dept_last_hire < ADDDATE(CURDATE(), INTERVAL -2 YEAR);
```

`MAX() OVER (PARTITION BY ...)` computes the same per-department maximum without collapsing rows, avoiding the need for a separate subquery and `IN` filter.

---

## ⚠️ Performance Considerations

- An index on `(department_id, hire_date)` lets the database compute each group's `MAX(hire_date)` very efficiently.
- The subquery must scan and group the whole table (or an index covering it) before the outer query can filter — for very large tables, consider materializing department-level "last activity" summaries.
- `ADDDATE(CURDATE(), INTERVAL -2 YEAR)` is evaluated fresh each time the query runs, so the cutoff always moves with the current date — good for staleness checks, but be aware the result set changes over time even with no data changes.
- `IN` with a subquery is usually optimized well by MySQL, but for very large ID lists a `JOIN` or `EXISTS` form can sometimes be faster — check the execution plan.

---

## 🚨 Common Mistakes

- Using `WHERE MAX(hire_date) < ...` instead of `HAVING` — aggregate functions can't be filtered in a `WHERE` clause.
- Forgetting that departments with zero employees never appear in the inner query at all, since there's nothing to `GROUP BY`.
- Assuming `MAX(hire_date)` ignores `NULL` hire dates the same way `COUNT(*)` would — a department with all-`NULL` hire dates produces a `NULL` max, which never satisfies `<`, silently excluding it from the "stale" list.
- Hardcoding a fixed cutoff date instead of computing it relative to `CURDATE()`, which makes the query's meaning drift out of sync with "today."
- Confusing "no recent activity" with "no activity at all" — this pattern finds departments that *have* history but it's old, not departments that never had any hires.

---

## 💡 Wisdom from Mímir

Silence is data too. A row that never arrives is often more informative than one that does — but a database can only tell you about what it *has* recorded, never directly about what it's still waiting for.

> "Has anything happened recently?" is really asking: **"How long has it been since the last thing happened?"**

Learn to translate absence-questions into `MAX()`-and-cutoff questions, and a whole category of "nothing is happening" problems becomes ordinary aggregation.

---

## 🔗 Related Notes

- [[Finding the Highest Salary in Each Department]]
- [[Finding the Group With the Highest Count]]
- [[Filtering Records by a Recent Date Window]]
- [[Filtering Employees by Length of Tenure]]
- [[Finding Groups Below a Size Threshold]]
