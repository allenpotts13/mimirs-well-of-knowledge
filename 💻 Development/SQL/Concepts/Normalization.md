---
type: concept
status: published
created: 2026-09-10
updated: 2026-09-10
technology: SQL
difficulty: Intermediate
tags:
  - database-design
  - normalization
aliases:
  - 1NF
  - 2NF
  - 3NF
  - Database Normalization
publish: true
---

# 📚 Normalization

> Organizing tables so each piece of data lives in exactly one place, eliminating redundancy and the update bugs it causes.

---

## 🎯 Purpose

Normalization structures a schema so updating a fact means updating it in one place, not hunting down every row that repeats it.

Use it when designing a schema from scratch, or when an existing table has repeated data causing inconsistent updates.

---

## 🧠 Key Ideas

- **1NF (First Normal Form)** — every column holds a single, atomic value; no repeating groups or comma-separated lists in one cell.
- **2NF (Second Normal Form)** — 1NF, plus every non-key column depends on the *whole* primary key (only matters for composite keys).
- **3NF (Third Normal Form)** — 2NF, plus no non-key column depends on another non-key column (no "transitive" dependencies).
- Higher normal forms exist (BCNF, 4NF...) but 3NF is where most practical schema design stops.
- **Denormalization** is the deliberate reverse — trading some redundancy for read speed, common in reporting/analytics tables.

---

## ⚙️ How It Works

Each normal form removes one specific kind of redundancy:

```text
Unnormalized:  Order table has CustomerName and CustomerEmail
               repeated on every single order row

1NF:           split multi-valued columns into atomic values

2NF:           split out data that only depends on part of a
               composite key into its own table

3NF:           split out data that depends on another non-key
               column (CustomerEmail depends on CustomerName,
               not on OrderID) into its own table
```

---

## 💻 Examples

```text
BEFORE (unnormalized):
Orders(OrderID, CustomerName, CustomerEmail, ProductName, Price)
→ Customer and product info repeats on every order row.

AFTER (normalized to 3NF):
Customers(CustomerID, CustomerName, CustomerEmail)
Products(ProductID, ProductName, Price)
Orders(OrderID, CustomerID, ProductID)
```

Now a customer's email only exists in one row, one place — update it once, and every order automatically reflects the change through the [[Foreign Keys|foreign key]].

---

## 🚀 Real World Applications

- Designing a new schema so data stays consistent as the application grows
- Fixing a table where updating one fact (like a customer's address) means updating dozens of duplicate rows
- Reducing storage by not repeating the same text values over and over

---

## ⚖️ Advantages

- Eliminates update anomalies — a fact only needs to change in one row.
- Reduces storage by not duplicating repeated values.
- Makes the schema's relationships explicit and self-documenting.

---

## ⚠️ Limitations

- More normalized schemas need more joins to reassemble a full picture, which can hurt read performance.
- Over-normalizing a reporting/analytics table can make simple queries unnecessarily complex — see denormalized star schemas for that use case.

---

## 🚨 Common Mistakes

- Repeating a customer's or product's details directly on every transaction row instead of referencing them by foreign key.
- Normalizing a reporting table the same way as a transactional one — OLTP and OLAP tables intentionally follow different rules.
- Stopping at 1NF and calling it done, leaving 2NF/3NF-style redundancy in place.

---

## 📖 Further Reading

Documentation

Books

Videos

Articles

---

## 💡 Wisdom from Mímir

Normalize the system that takes writes (the transactional database). Denormalize the system that serves reads (a reporting table or data warehouse). Treating both the same way is a common source of either update bugs or slow reports.

---

## 🔗 Related Notes

- [[Primary Keys]]
- [[Foreign Keys]]
- [[Constraints]]
