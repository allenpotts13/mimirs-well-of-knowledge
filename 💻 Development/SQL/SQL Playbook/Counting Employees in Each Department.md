---
type: query-pattern
status: published
created: 2026-08-13
updated: 2026-08-13
technology: SQL
difficulty: Beginner
tags:
  - group-by
  - count
  - aggregate-functions
  - reporting
aliases:
  - Employee Count by Department
  - COUNT by Department
publish: true
---

# <span class="rune">ᚲ</span> Counting Employees in Each Department

> *Use `GROUP BY` with `COUNT()` to determine how many records belong to each category.*

---

## 🎯 Problem

Find the number of employees in each department.

---

## 🤔 Mental Model

Think of the employees as being placed into separate buckets based on their department.

```text
Department 10 → 👤 👤 👤 👤
Department 20 → 👤 👤
Department 30 → 👤 👤 👤 👤 👤
```

`GROUP BY` creates the buckets.

`COUNT()` counts what's inside each bucket.


---

## 🧠 Why This Pattern Works

Two operations are working together:

```sql
GROUP BY department_id
```

creates one group for each department.

```sql
COUNT(*)
```

counts the rows within each group.


---

## 💻 SQL Solution

```sql
SELECT
    department_id,
    COUNT(*) AS employee_count
FROM employees
GROUP BY department_id;
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Identify the grouping column

```sql
department_id
```

### Step 2 — Group the records

```sql
GROUP BY department_id
```

### Step 3 — Count each group

```sql
COUNT(*)
```

The result might look like:

| department_id | employee_count |
|---:|---:|
| 10 | 15 |
| 20 | 32 |
| 30 | 8 |

---

## 🚀 Common Use Cases

- Employees per department
- Orders per customer
- Products per category
- Tickets per technician
- Incidents per location
- Transactions per account

---

## ⚖️ Alternatives

```sql
SELECT
    department_id,
    COUNT(employee_id) AS employee_count
FROM employees
GROUP BY department_id;
```

`COUNT(employee_id)` counts only rows where `employee_id` isn't `NULL`, whereas `COUNT(*)` counts rows.

---

## ⚠️ Performance Considerations

- `GROUP BY` requires the database to group qualifying rows.
- An index on `department_id` may help depending on the database and execution plan.
- Filter rows with `WHERE` before grouping when possible.
- On very large datasets, aggregation can become CPU- or memory-intensive.

---

## 🚨 Common Mistakes

- Forgetting the `GROUP BY`.
- Grouping by the wrong column.
- Using `COUNT(column)` when `COUNT(*)` is intended.
- Selecting columns that aren't grouped or aggregated.

---

## 💡 Wisdom from Mímir

When the question says:

> **"How many of X are in each Y?"**

think:

```text
GROUP BY Y
+
COUNT(X)
```

This is one of the most fundamental SQL patterns to recognize.

---

## 🔗 Related Notes

- [[Finding the Group With the Highest Count]] — takes this exact count and ranks groups by it
- [[Calculating Multiple Aggregates Per Group]] — computing several aggregates (not just COUNT) per group in one query
- [[Calculating Each Group's Percentage of the Total]] — turns each group's count into a share of the whole table