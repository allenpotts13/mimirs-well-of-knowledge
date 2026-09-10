---
type: query-pattern
status: active
created: 2026-08-13
updated: 2026-08-13
technology: SQL
difficulty: Beginner
tags:
  - query-pattern
  - where
  - filtering
aliases:
  - Employees by Manager
  - Direct Reports
  - Find Employees by Manager
---

# 🧩 Find All Employees Who Report to a Specific Manager

> *A reusable SQL solution for finding employees who report directly to a specific manager.*

---

## 🎯 Problem

How can we find all employees who report to a specific manager?

---

## 🤔 Mental Model

Each employee has a `manager_id`.

If an employee's `manager_id` matches the ID of the manager we're looking for, that employee is a direct report of that manager.


---

## 🧠 Why This Pattern Works

We don't need a complicated query when the employee table already contains the manager relationship.

We simply filter `manager_id` to the desired manager.

---

## 💻 SQL Solution

```sql
SELECT *
FROM employees
WHERE manager_id = ?;
```

The `?` represents the manager ID supplied by the application or user.

---

## 🔄 Step-by-Step Breakdown

1. Start with the `employees` table.
2. Look at the `manager_id` column.
3. Compare `manager_id` to the target manager.
4. Return every employee whose `manager_id` matches.

---

## 🚀 Common Use Cases

- Organizational reporting
- Employee directories
- Management dashboards
- HR reporting
- Organizational charts

---

## ⚖️ Alternatives

If manager information is stored in the same employee table and you need the manager's name, use a self-join:

```sql
SELECT
    e.employee_id,
    e.name,
    m.name AS manager_name
FROM employees e
JOIN employees m
    ON e.manager_id = m.employee_id
WHERE m.employee_id = ?;
```

---

## ⚠️ Performance Considerations

- An index on `manager_id` can make this lookup very efficient.
- Indexing becomes increasingly useful as the employee table grows.
- Avoid unnecessary joins when the required information is already available.

---

## 🚨 Common Mistakes

- Filtering on `employee_id` instead of `manager_id`.
- Confusing the manager's ID with the employee IDs of their reports.
- Forgetting that `NULL` may represent employees without a manager.
- Hard-coding values when parameters should be used.

---

## 💡 Wisdom from Mímir

> _"Understand the relationship column before reaching for a JOIN."_

Sometimes the simplest query is the correct one.

---

## 🔗 Related Notes

- [[Finding Employees With No Subordinates]] — the same manager_id/employee_id hierarchy, checking for the absence of reports instead of listing them
- [[Finding Peers Who Share the Same Manager]] — a closely related question: employees grouped by manager rather than filtered by a specific one
- [[Finding Groups Below a Size Threshold]] — extends this idea to "managers with more/fewer than N reports"
- [[Comparing Related Rows With a Self-Join]] — the self-join shown in this note's Alternatives, generalized to compare any attribute between an employee and their manager