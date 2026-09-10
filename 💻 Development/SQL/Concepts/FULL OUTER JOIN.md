---
type: concept
status: active
created: 2026-09-10
updated: 2026-09-10
technology: SQL
difficulty: Intermediate
tags:
  - joins
  - relational-database
aliases:
  - SQL FULL OUTER JOIN
  - FULL JOIN
---

# 📚 FULL OUTER JOIN

> Returns every row from both tables, matching where possible and filling in NULL wherever one side has nothing to match.

---

## 🎯 Purpose

FULL OUTER JOIN combines [[LEFT JOIN]] and [[RIGHT JOIN]] — nothing from either table is ever dropped, matched or not.

Use it when both sides of the relationship might have rows the other side doesn't, and every one of them needs to show up.

---

## 🧠 Key Ideas

- Every row from the left table appears, matched or not.
- Every row from the right table appears, matched or not.
- Unmatched rows from either side show NULL for the other table's columns.
- Not supported natively by every database engine — MySQL has historically required simulating it with a `UNION` of a LEFT JOIN and a RIGHT JOIN.

---

## ⚙️ How It Works

```text
LEFT JOIN:        all of A + matches from B
RIGHT JOIN:       all of B + matches from A
FULL OUTER JOIN:  all of A + all of B, matched where possible
```

---

## 💻 Examples

```sql
SELECT
	c.CustomerName,
	o.OrderDate
FROM Customers c
FULL OUTER JOIN Orders o
	ON c.CustomerID = o.CustomerID;
```

This returns matched customer/order pairs, customers with no orders (NULL order columns), and orders with no matching customer (NULL customer columns) — all in one result set.

On engines without native support:

```sql
SELECT c.CustomerName, o.OrderDate FROM Customers c LEFT JOIN Orders o ON c.CustomerID = o.CustomerID
UNION
SELECT c.CustomerName, o.OrderDate FROM Customers c RIGHT JOIN Orders o ON c.CustomerID = o.CustomerID;
```

---

## 🚀 Real World Applications

- Reconciling two datasets that should mostly overlap (e.g. comparing two systems' customer lists)
- Finding mismatches in both directions at once — "orders with no customer" and "customers with no orders" together
- Data migration validation

---

## ⚖️ Advantages

- Guarantees complete visibility into both tables in a single query.
- Surfaces mismatches from both directions at once, instead of running two separate queries.

---

## ⚠️ Limitations

- Not supported natively everywhere (notably older MySQL versions).
- Produces more NULLs to handle than any other join type — application code needs to check both sides.
- Can return a very large result set on big, loosely related tables.

---

## 🚨 Common Mistakes

- Reaching for FULL OUTER JOIN when a simple LEFT JOIN would answer the question — it's the least common join for a reason.
- Forgetting `UNION` (not `UNION ALL`) is needed when simulating it manually, to avoid duplicating the matched rows.

---

## 📖 Further Reading

Documentation

Books

Videos

Articles

---

## 💡 Wisdom from Mímir

FULL OUTER JOIN is a reconciliation tool, not a default. If the question is really "what does table A have," reach for LEFT JOIN — it's simpler to reason about and every engine supports it.

---

## 🔗 Related Notes

- [[LEFT JOIN]]
- [[RIGHT JOIN]]
- [[INNER JOIN]]
- [[CROSS JOIN]]
