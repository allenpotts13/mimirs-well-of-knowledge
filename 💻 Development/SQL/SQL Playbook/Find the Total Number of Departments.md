---
type: query-pattern
status: published
created: 2026-08-13
updated: 2026-08-13
technology: SQL
difficulty: Beginner
tags:
  - query-pattern
  - count
  - distinct
aliases:
  - Count Departments
  - Number of Departments
publish: true
---

# 🧩 Find the Total Number of Departments

> *A reusable SQL solution for counting unique departments represented in a table.*

---

## 🎯 Problem

How can we find the total number of departments represented in the company?

---

## 🤔 Mental Model

Multiple employees can belong to the same department.

Therefore, counting rows would count employees rather than departments.

We need to count each unique `department_id` only once.

---

## 🧠 Why This Pattern Works

`DISTINCT` removes duplicate department IDs before `COUNT` counts them.

```sql
COUNT(DISTINCT department_id)
```

means:

> Count each unique department only once.

---

## 💻 SQL Solution

```sql
SELECT COUNT(DISTINCT department_id) AS department_count
FROM employees;
```

---

## 🔄 Step-by-Step Breakdown

1. Read the `department_id` values.
2. Remove duplicate department IDs with `DISTINCT`.
3. Count the remaining unique department IDs.

---

## 🚀 Common Use Cases

- Company reporting
- Department summaries
- Dashboard metrics
- Organizational analysis
- Data validation

---

## ⚖️ Alternatives

If the database has a dedicated `departments` table:

```sql
SELECT COUNT(*)
FROM departments;
```

This may be more accurate because it counts departments defined by the organization rather than departments currently represented by employees.

---

## ⚠️ Performance Considerations

- `COUNT(DISTINCT ...)` may require additional work to identify unique values.
- An index on `department_id` may improve performance.
- Large datasets may require more memory or processing for the distinct operation.
- Check the execution plan for very large tables.

---

## 🚨 Common Mistakes

- Using `COUNT(*)` and accidentally counting employees.
- Forgetting `DISTINCT`.
- Assuming every department has an employee.
- Counting from the employee table when a dedicated department table should be used.

---

## 💡 Wisdom from Mímir

When counting things represented by repeated rows, ask whether you need rows or unique entities.

---

## 🔗 Related Notes

- [[Counting Employees in Each Department]] — counts rows per department instead of the number of distinct departments
- [[Calculating Each Group's Percentage of the Total]] — uses this same "count vs. total" framing, one level down at the per-group share instead of the overall unique count