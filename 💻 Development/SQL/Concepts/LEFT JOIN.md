---
type: concept
status: published
created: 2026-09-10
updated: 2026-09-10
technology: SQL
difficulty: Beginner
tags:
  - joins
  - relational-database
aliases:
  - SQL LEFT JOIN
  - LEFT OUTER JOIN
publish: true
---

# 📚 LEFT JOIN

> Returns every row from the left table, plus matching rows from the right table — filling in NULL where nothing matches.

---

## 🎯 Purpose

LEFT JOIN preserves every row from the first (left) table, even when there's no matching row in the second (right) table.

Use it when the left table's rows all matter, whether or not they have a related record.

---

## 🧠 Key Ideas

- Keeps every row from the left table.
- Unmatched rows show NULL for every right-table column.
- Table order matters — swapping tables changes the result.
- The classic tool for finding what's *missing* on the right side.

---

## ⚙️ How It Works

The database evaluates the ON condition just like an INNER JOIN.

Where a match exists, columns from both tables are combined.

Where no match exists, the row from the left table is still returned — the right table's columns are just filled with NULL.

---

## 💻 Examples

```sql
SELECT
	c.CustomerName,
	o.OrderDate
FROM Customers c
LEFT JOIN Orders o
	ON c.CustomerID = o.CustomerID;
```

Add `WHERE o.OrderDate IS NULL` to isolate customers with *no* orders at all — see [[Finding Records With a Missing Relationship]].

---

## 🚀 Real World Applications

- Customers who have never placed an order
- Employees with no assigned department
- Optional one-to-many relationships in a report
- Auditing for orphaned or missing related data

---

## ⚖️ Advantages

- Never silently drops rows from the table that matters most.
- Makes "what's missing" questions easy to answer.
- Same performance characteristics as INNER JOIN when indexed properly.

---

## ⚠️ Limitations

- Can produce a lot of NULLs to handle in application code.
- Easy to accidentally turn back into an INNER JOIN (see Common Mistakes).

---

## 🚨 Common Mistakes

- Filtering the right table in the WHERE clause instead of the ON clause — a WHERE filter on a NULL-able column silently discards the unmatched rows and turns the LEFT JOIN into an INNER JOIN.
- Forgetting which table is "left" after several joins are chained together.
- Assuming aggregate functions ignore the NULLs correctly without double-checking (`COUNT(o.OrderID)` vs `COUNT(*)` behave differently here).

---

## 📖 Further Reading

Documentation

Books

Videos

Articles

---

## 💡 Wisdom from Mímir

When a LEFT JOIN unexpectedly returns the same row count as an INNER JOIN would, the ON clause is usually fine — check the WHERE clause first. That's almost always where the join quietly gets undone.

---

## 🔗 Related Notes

- [[INNER JOIN]]
- [[RIGHT JOIN]]
- [[Finding Records With a Missing Relationship]]
- [[Indexes]]
