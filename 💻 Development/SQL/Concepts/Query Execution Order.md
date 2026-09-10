---
type: concept
status: published
created: 2026-09-10
updated: 2026-09-10
technology: SQL
difficulty: Intermediate
tags:
  - query-structure
  - fundamentals
aliases:
  - Logical Query Processing Order
  - SQL Execution Order
publish: true
permalink: sql/query-execution-order
---

# <span class="rune">ᛟ</span> Query Execution Order

> SQL is written in one order but executed in a completely different one — knowing the real order explains a lot of "why doesn't this work" moments.

---

## 🎯 Purpose

Understanding the actual logical processing order explains why a column alias can't be used in `WHERE`, why `HAVING` can filter on aggregates but `WHERE` can't, and why `LIMIT`/`TOP` applies last.

---

## 🧠 Key Ideas

- Written order: `SELECT → FROM → WHERE → GROUP BY → HAVING → ORDER BY → LIMIT`
- **Actual logical execution order:**

```text
1. FROM        — identify the source table(s)
2. JOIN        — combine with other tables
3. WHERE       — filter individual rows
4. GROUP BY    — collapse rows into groups
5. HAVING      — filter groups
6. SELECT      — compute the output columns
7. DISTINCT    — remove duplicate output rows
8. ORDER BY    — sort the final result
9. LIMIT/TOP   — cut down to the requested row count
```

- `SELECT` runs *after* `WHERE`, which is exactly why a column alias defined in `SELECT` can't be referenced in `WHERE` — it doesn't exist yet at that stage.
- `ORDER BY` runs almost last, which is why it *can* reference a `SELECT` alias.

---

## ⚙️ How It Works

The database doesn't execute the clauses in the order they're typed — it always resolves `FROM`/`JOIN` first to know what data it's working with, filters before grouping, groups before selecting output columns, and sorts only at the very end.

---

## 💻 Examples

```sql
SELECT
	Department,
	COUNT(*) AS EmployeeCount
FROM Employees
WHERE HireDate >= '2020-01-01'   -- step 3: filters rows first
GROUP BY Department               -- step 4: collapses into groups
HAVING COUNT(*) > 5               -- step 5: filters the groups
ORDER BY EmployeeCount DESC;      -- step 8: sorts the final output
```

`EmployeeCount` doesn't exist yet during `WHERE` or `GROUP BY` — it's only computed at the `SELECT` step, which is why `WHERE EmployeeCount > 5` would fail but `HAVING COUNT(*) > 5` works fine.

---

## 🚀 Real World Applications

- Debugging "column doesn't exist" errors involving an alias
- Understanding why `HAVING` is needed instead of `WHERE` for aggregate filters — see [[GROUP BY and HAVING]]
- Reasoning about where a window function's `OVER()` clause fits relative to `WHERE`/`GROUP BY`

---

## ⚖️ Advantages

- Once internalized, most "why won't this query work" confusion disappears.
- Explains several SQL rules that otherwise look arbitrary.

---

## ⚠️ Limitations

- The mental model is universal, but exact optimizer behavior (what actually executes first *physically*, for performance) can differ from the logical order — the logical order is about correctness, not execution speed.

---

## 🚨 Common Mistakes

- Trying to reference a `SELECT`-defined alias inside `WHERE` and being confused why it fails.
- Writing a filter condition on an aggregate into `WHERE` instead of `HAVING`.
- Assuming `LIMIT`/`TOP` applies before sorting — it always applies after `ORDER BY`.

---

## 📖 Further Reading

Documentation

Books

Videos

Articles

---

## 💡 Wisdom from Mímir

When a query throws a confusing "invalid column" or "aggregate not allowed here" error, mentally walk through the execution order — `FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY` — and check whether the thing being referenced actually exists yet at that stage.

---

## 🔗 Related Notes

- [[GROUP BY and HAVING]]
- [[Subqueries]]
- [[Window Functions]]
