---
type: concept
status: published
created: 2026-09-10
updated: 2026-09-10
technology: SQL
difficulty: Beginner
tags:
  - aggregation
  - fundamentals
aliases:
  - COUNT SUM AVG MIN MAX
  - Aggregate Function
publish: true
---

# 📚 Aggregate Functions

> Collapse many rows into a single summary value — the building blocks behind every total, average, and count.

---

## 🎯 Purpose

Aggregate functions compute one value from a set of rows — a total, an average, a count, an extreme. They're the foundation [[GROUP BY and HAVING|GROUP BY]] and [[Window Functions|window functions]] both build on.

---

## 🧠 Key Ideas

- **COUNT** — number of rows (or non-NULL values in a specific column).
- **SUM** — total of a numeric column.
- **AVG** — mean of a numeric column.
- **MIN** / **MAX** — smallest/largest value in a column.
- Used alone, an aggregate collapses the *entire* table into one row.
- Combined with `GROUP BY`, it collapses each group into one row instead.
- Every aggregate except `COUNT(*)` ignores NULL values — see [[NULL and Three-Valued Logic]].

---

## ⚙️ How It Works

Without `GROUP BY`, an aggregate function treats the whole table as a single group:

```sql
SELECT AVG(Price) FROM Products;   -- one row, one number
```

With `GROUP BY`, it computes the aggregate separately per group:

```sql
SELECT Category, AVG(Price)
FROM Products
GROUP BY Category;                 -- one row per category
```

---

## 💻 Examples

```sql
SELECT
	COUNT(*) AS TotalOrders,
	SUM(TotalAmount) AS Revenue,
	AVG(TotalAmount) AS AvgOrderValue,
	MIN(OrderDate) AS FirstOrder,
	MAX(OrderDate) AS LastOrder
FROM Orders;
```

---

## 🚀 Real World Applications

- Total revenue, average order value, order counts
- Earliest/latest date in a dataset
- Per-category or per-department summaries when paired with `GROUP BY`

---

## ⚖️ Advantages

- Compact, declarative way to summarize large amounts of data.
- Every major SQL engine implements the same core set consistently.

---

## ⚠️ Limitations

- Collapses row-level detail — use a [[Window Functions|window function]] instead when both the detail rows and the aggregate are needed together.
- `AVG`/`SUM`/`MIN`/`MAX` silently ignore NULLs, which can skew results if that's not expected.

---

## 🚨 Common Mistakes

- Using `COUNT(Column)` when `COUNT(*)` was meant, or vice versa — one ignores NULLs in that column, the other counts every row regardless.
- Selecting a non-aggregated, non-grouped column alongside an aggregate — see [[GROUP BY and HAVING]] for why this fails or returns unpredictable results.
- Forgetting `AVG()` divides by the count of non-NULL values, not the total row count — a column with NULLs can produce a higher average than expected.

---

## 📖 Further Reading

Documentation

Books

Videos

Articles

---

## 💡 Wisdom from Mímir

Before trusting an `AVG()` or `SUM()`, check whether the underlying column actually has NULLs — a silently skipped NULL can make an average look better (or worse) than the real picture.

---

## 🔗 Related Notes

- [[GROUP BY and HAVING]]
- [[Window Functions]]
- [[NULL and Three-Valued Logic]]
