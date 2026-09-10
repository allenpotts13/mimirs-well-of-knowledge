---
type: query-pattern
status: published
created: 2026-09-02
updated: 2026-09-02
technology: SQL
difficulty: Beginner
tags:
  - query-pattern
  - null
  - where
aliases:
  - IS NULL
  - Finding missing values
publish: true
---

# 🧩 Filtering Rows With a NULL Column

> *Test for missing values the only way SQL actually allows: IS NULL.*

---

## 🎯 Problem

How do you fetch records where a column has no value — for example, employees who don't have a bonus?

---

## 💻 SQL Solution

```sql
SELECT * FROM employees WHERE column_name IS NULL;

-- Real example: employees with no bonus
SELECT * FROM employees WHERE bonus IS NULL;
```

---

## 📝 Notes

**Headline gotcha: always use `IS NULL` / `IS NOT NULL`, never `= NULL` or `!= NULL`.** In standard SQL, `NULL` represents "unknown," and any comparison against it — including `= NULL` — evaluates to unknown, which is treated as false. A query written as `WHERE bonus = NULL` will silently return zero rows even when NULL bonuses exist, with no error to warn you. This is one of the most common beginner mistakes in SQL, so it's worth internalizing early.

- `IS NOT NULL` is the inverse, for rows that do have a value.
- Watch for this trap inside `NOT IN` subqueries too — a single NULL in the subquery's result set can make the whole `NOT IN` return nothing.

---

## 🔗 Related Notes

- [[Finding Records With a Missing Relationship]]
