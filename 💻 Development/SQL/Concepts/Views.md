---
type: concept
status: published
created: 2026-09-10
updated: 2026-09-10
technology: SQL
difficulty: Beginner
tags:
  - views
  - database-objects
aliases:
  - SQL View
  - Materialized View
publish: true
permalink: sql/views
---

# <span class="rune">ᛟ</span> Views

> A saved query that behaves like a table — the underlying SQL runs fresh every time it's queried.

---

## 🎯 Purpose

A view packages a complex or frequently-used query behind a simple name, so people and applications can query it like a regular table without repeating the underlying logic.

Use it to hide complexity, standardize a commonly-needed shape of data, or restrict which columns/rows a user can see.

---

## 🧠 Key Ideas

- A standard view stores no data — it re-runs its defining query every time it's selected from.
- A **materialized view** (supported by some engines) *does* store the result physically, trading freshness for speed, and needs an explicit refresh.
- Views can join, filter, and aggregate underlying tables into a simpler shape.
- Can be used to restrict access — grant permissions on the view instead of the raw table, exposing only certain columns/rows.

---

## ⚙️ How It Works

```sql
CREATE VIEW ActiveCustomers AS
SELECT CustomerID, CustomerName, Email
FROM Customers
WHERE Status = 'Active';
```

From here on, `SELECT * FROM ActiveCustomers` runs the underlying query behind the scenes, every single time.

---

## 💻 Examples

```sql
CREATE VIEW DepartmentSummary AS
SELECT
	Department,
	COUNT(*) AS EmployeeCount,
	AVG(Salary) AS AvgSalary
FROM Employees
GROUP BY Department;

SELECT * FROM DepartmentSummary WHERE EmployeeCount > 10;
```

---

## 🚀 Real World Applications

- Simplifying a query that joins 5+ tables into one clean, reusable name
- Giving a reporting tool a stable, simplified interface into a complex schema
- Restricting a group of users to only certain columns or rows of a sensitive table

---

## ⚖️ Advantages

- Keeps complex logic in one defined place instead of copy-pasted across many queries.
- Can enforce column/row-level access control without duplicating data.
- Underlying tables can change structure without breaking consumers, as long as the view's output shape stays the same.

---

## ⚠️ Limitations

- A standard view adds no performance benefit — it's a shortcut, not a cache.
- Views built on views ("nested views") can get hard to trace and can hurt performance further.
- Not all views are updatable (writable) — it depends on how complex the underlying query is.

---

## 🚨 Common Mistakes

- Expecting a standard view to be faster than the raw query — it isn't, since it runs the same query underneath.
- Stacking views on top of views several layers deep, making it hard to trace what's actually being computed.
- Forgetting a materialized view needs an explicit refresh and can silently serve stale data.

---

## 📖 Further Reading

Documentation

Books

Videos

Articles

---

## 💡 Wisdom from Mímir

A view is a readability and access-control tool, not a performance tool. If the goal is speed, look at [[Indexes]] or a materialized view with a refresh strategy — a plain view just hides the same work behind a friendlier name.

---

## 🔗 Related Notes

- [[Common Table Expressions]]
- [[Stored Procedures]]
