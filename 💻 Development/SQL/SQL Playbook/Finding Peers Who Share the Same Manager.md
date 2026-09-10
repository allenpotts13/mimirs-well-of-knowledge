---
type: query-pattern
status: published
created: 2026-09-02
updated: 2026-09-02
technology: SQL
difficulty: Beginner
tags:
  - query-pattern
  - self-join
  - subquery
  - hierarchical-data
aliases:
  - Employees under the same manager
publish: true
---

# 🧩 Finding Peers Who Share the Same Manager

> *Group employees by shared manager using a self-referencing subquery on manager_id.*

---

## 🎯 Problem

How do you list all employees who work under the same manager as at least one other employee?

---

## 💻 SQL Solution

```sql
SELECT * FROM employees
WHERE manager_id IN (
  SELECT manager_id FROM employees WHERE manager_id IS NOT NULL
);
```

---

## 📝 Notes

- This relies on the same self-referencing `manager_id` column found throughout the employees table's hierarchy — the same column that powers [[Finding Employees With No Subordinates]] and [[Finding Groups Below a Size Threshold]], just answering a different question (peer grouping here, vs. leaf-detection or headcount thresholds there).
- The `IS NOT NULL` filter matters — otherwise top-level employees with no manager (`manager_id IS NULL`) would leak into the subquery and break the `IN` comparison (see [[Filtering Rows With a NULL Column]]).
- To actually group peers together rather than just list everyone with a manager, add `ORDER BY manager_id` or aggregate with `GROUP BY manager_id`.

---

## 🔗 Related Notes

- [[Finding Employees With No Subordinates]]
- [[Finding Groups Below a Size Threshold]]
- [[Find All Employees Who Report to a Specific Manager]]
