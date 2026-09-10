---
type: concept
status: published
created: 2026-09-10
updated: 2026-09-10
technology: SQL
difficulty: Intermediate
tags:
  - indexes
  - performance
aliases:
  - Database Indexes
  - B-Tree Index
publish: true
permalink: sql/indexes
---

# <span class="rune">ᛟ</span> Indexes

> A lookup structure that lets the database jump straight to matching rows instead of scanning the whole table.

---

## 🎯 Purpose

Indexes exist to make reads fast on tables too large to scan row-by-row every time.

Use them on columns that are searched, joined, or sorted on frequently — not on every column "just in case."

---

## 🧠 Key Ideas

- Typically implemented as a B-tree — a sorted structure that supports fast lookups, range scans, and ordered retrieval.
- The primary key is indexed automatically in almost every database engine.
- Indexes speed up reads but slow down writes — every INSERT/UPDATE/DELETE has to update the index too.
- A **composite index** covers multiple columns together, and column order in the index matters.
- Indexes cost storage — they're a duplicate, sorted copy of the indexed columns.

---

## ⚙️ How It Works

Without an index, the database performs a **full table scan** — checking every row to see if it matches.

With an index, the database walks the sorted structure to find matching rows directly, similar to using a book's index instead of reading every page.

```text
No index:    scan all 1,000,000 rows
With index:  jump directly to the ~50 matching rows
```

---

## 💻 Examples

```sql
CREATE INDEX idx_customers_email
ON Customers (Email);

-- Composite index — order matters
CREATE INDEX idx_orders_customer_date
ON Orders (CustomerID, OrderDate);
```

---

## 🚀 Real World Applications

- Speeding up `WHERE` lookups on large tables
- Speeding up `JOIN` conditions (index the foreign key column)
- Speeding up `ORDER BY` so results are already sorted
- Enforcing uniqueness (a unique index doubles as a constraint)

---

## ⚖️ Advantages

- Can turn a multi-second query into a millisecond one on large tables.
- Composite indexes can satisfy multiple query patterns at once.
- Costs nothing at query-write time — the optimizer picks it up automatically once it exists.

---

## ⚠️ Limitations

- Every index slows down writes to that table.
- Indexes consume disk space, sometimes significantly.
- An index the query planner can't use (wrong column order, function wrapped around the column) provides no benefit at all.

---

## 🚨 Common Mistakes

- Indexing every column defensively — this bloats storage and slows every write for little read benefit.
- Not indexing foreign key columns used in joins — see [[Missing Indexes]].
- Expecting an index to help a leading-wildcard search (`LIKE '%something'`) — it generally can't, since the sort order doesn't help when the match could start anywhere.
- Building a composite index in the wrong column order for the actual query patterns in use.

---

## 📖 Further Reading

Documentation

Books

Videos

Articles

---

## 💡 Wisdom from Mímir

When a query is slow, check the execution plan before adding an index blindly — see [[Slow Queries]]. An index only helps if the planner actually chooses to use it, and the wrong index can sit there doing nothing but slowing down every write.

---

## 🔗 Related Notes

- [[Missing Indexes]]
- [[Slow Queries]]
- [[Primary Keys]]
- [[INNER JOIN]]
