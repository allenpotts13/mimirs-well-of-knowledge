---
type: concept
status: published
created: 2026-09-10
updated: 2026-09-10
technology: SQL
difficulty: Intermediate
tags:
  - window-functions
  - ranking
  - analytics
aliases:
  - OVER clause
  - PARTITION BY
publish: true
---

# 📚 Window Functions

> Calculate across a set of related rows without collapsing them into one row — unlike GROUP BY, every original row survives.

---

## 🎯 Purpose

Window functions let a query answer questions like "what's this row's rank within its group" or "what's the running total up to this row," while still returning one output row per input row.

Use them whenever GROUP BY would give the right *number* but destroy row-level detail you still need.

---

## 🧠 Key Ideas

- Built around the `OVER()` clause.
- `PARTITION BY` defines the group a row is compared against (like GROUP BY, but without collapsing rows).
- `ORDER BY` inside `OVER()` defines the order rows are processed in — required for ranking and running-total functions.
- Common functions: `ROW_NUMBER()`, `RANK()`, `DENSE_RANK()`, `LAG()`, `LEAD()`, and aggregates like `SUM()`/`AVG()` used as windows instead of GROUP BY.

---

## ⚙️ How It Works

For each row, the database defines a "window" of related rows using `PARTITION BY` (which rows count as the same group) and `ORDER BY` (what order to process them in).

The function then computes its result using only that window — but the original row is still returned, just with the extra calculated column attached.

```text
GROUP BY:          5 rows in  →  1 row out per group
Window function:   5 rows in  →  5 rows out, each annotated
```

---

## 💻 Examples

```sql
SELECT
	employee_name,
	department,
	salary,
	RANK() OVER (
		PARTITION BY department
		ORDER BY salary DESC
	) AS salary_rank
FROM employees;
```

Every employee row is kept — each just gets a rank relative to their own department.

---

## 🚀 Real World Applications

- Leaderboards and rankings within a category
- Running totals (cumulative sales by day)
- Period-over-period comparisons (`LAG()` to compare this month to last month)
- Deduplication (rank duplicates, keep only rank = 1)

---

## ⚖️ Advantages

- Keeps row-level detail *and* group-level context in the same result set.
- Replaces what would otherwise need a self-join or a correlated subquery.
- One query can compute multiple different window calculations side by side.

---

## ⚠️ Limitations

- Can be more expensive than a simple aggregate on very large tables.
- Syntax and supported functions vary slightly between database engines.
- Harder to reason about than GROUP BY at first — the mental model takes some getting used to.

---

## 🚨 Common Mistakes

- Confusing `PARTITION BY` with `GROUP BY` — partitioning doesn't reduce row count.
- Forgetting `ORDER BY` inside `OVER()` for ranking functions, which makes the rank meaningless.
- Reaching for `RANK()` when `DENSE_RANK()` is meant (or vice versa) — `RANK()` leaves gaps after ties, `DENSE_RANK()` doesn't.
- Using `ROW_NUMBER()` when ties should share a rank — it always assigns a unique number, even to identical values.

---

## 📖 Further Reading

Documentation

Books

Videos

Articles

---

## 💡 Wisdom from Mímir

Before writing the window function, decide in plain English what "tied values" should do — share a position, get gapped positions, or get arbitrarily split apart. That decision picks the function for you: `DENSE_RANK()`, `RANK()`, or `ROW_NUMBER()`.

---

## 🔗 Related Notes

- [[Finding the Nth Highest Value]]
- [[Finding the Second Highest Value]]
- [[Ranking the Top N Rows Within Each Group]]
- [[INNER JOIN]]
