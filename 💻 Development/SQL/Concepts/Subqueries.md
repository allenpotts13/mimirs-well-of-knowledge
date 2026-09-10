---
type: concept
status: active
created: 2026-09-10
updated: 2026-09-10
technology: SQL
difficulty: Intermediate
tags:
  - subqueries
  - query-structure
aliases:
  - Nested Query
  - Correlated Subquery
---

# 📚 Subqueries

> A query nested inside another query, used to feed a value, a list, or a filtered set into the outer query.

---

## 🎯 Purpose

Subqueries let one query use the result of another as part of its own logic — a filter, a computed column, or a virtual table to join against.

Use them when a value needed for filtering or comparison has to be *calculated* rather than looked up directly.

---

## 🧠 Key Ideas

- Can appear in `SELECT`, `FROM`, `WHERE`, or `HAVING`.
- A **non-correlated** subquery runs once, independently of the outer query.
- A **correlated** subquery references a column from the outer query and re-runs once per outer row — more expensive, but sometimes the clearest way to express the logic.
- Can be swapped for a [[Common Table Expressions|CTE]] or a join in many cases — often a style/readability choice, sometimes a real performance difference.

---

## ⚙️ How It Works

The database evaluates the inner query (once, or once per outer row if correlated) and uses its result wherever the subquery appears — as a single value, a list, or a full result set.

```text
Non-correlated:  inner query runs once → result reused for every outer row
Correlated:      inner query re-runs for every single outer row
```

---

## 💻 Examples

```sql
-- Non-correlated: runs once
SELECT ProductName
FROM Products
WHERE Price > (SELECT AVG(Price) FROM Products);

-- Correlated: re-evaluated per outer row
SELECT e.EmployeeName
FROM Employees e
WHERE e.Salary > (
	SELECT AVG(Salary)
	FROM Employees e2
	WHERE e2.Department = e.Department
);
```

The correlated version answers "who earns more than *their own department's* average" — see [[Filtering Rows Above Their Group's Average]] for the window-function alternative.

---

## 🚀 Real World Applications

- Filtering against a calculated value (above-average price, latest date)
- Checking existence with `EXISTS` / `NOT EXISTS`
- Building a derived table in the `FROM` clause to join against

---

## ⚖️ Advantages

- Expresses "compare against a calculated value" clearly and directly.
- Can break a complex problem into a readable inner and outer piece.
- `EXISTS` subqueries are often more efficient than an equivalent join when only checking for presence.

---

## ⚠️ Limitations

- Correlated subqueries can be slow on large tables since they re-run per row.
- Deeply nested subqueries get hard to read — a [[Common Table Expressions|CTE]] is usually clearer past one or two levels.
- Some subquery patterns the optimizer can't rewrite as efficiently as an equivalent join.

---

## 🚨 Common Mistakes

- Using a correlated subquery where a window function (see [[Window Functions]]) or a join would be faster and just as clear.
- Returning more than one row from a subquery used in a context expecting a single value, causing a runtime error.
- Nesting subqueries several levels deep instead of naming each step with a CTE.

---

## 📖 Further Reading

Documentation

Books

Videos

Articles

---

## 💡 Wisdom from Mímir

If a subquery is hard to read, that's usually a sign it wants to be a [[Common Table Expressions|CTE]] instead — same logic, but named and readable top-to-bottom rather than nested inside parentheses.

---

## 🔗 Related Notes

- [[Common Table Expressions]]
- [[Window Functions]]
- [[Filtering Rows Above Their Group's Average]]
