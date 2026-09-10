---
type: concept
status: active
created: 2026-09-10
updated: 2026-09-10
technology: SQL
difficulty: Beginner
tags:
  - joins
  - relational-database
aliases:
  - SQL CROSS JOIN
  - Cartesian Product
---

# 📚 CROSS JOIN

> Pairs every row in one table with every row in another — no matching condition, just every possible combination.

---

## 🎯 Purpose

CROSS JOIN produces the **Cartesian product** of two tables: if table A has 3 rows and table B has 4, the result has 12 rows — every combination of one row from each.

Use it deliberately when every combination is actually needed, not by accident from a forgotten `ON` clause.

---

## 🧠 Key Ideas

- No `ON` condition — every row pairs with every other row.
- Result size is the product of both table sizes, which grows fast.
- Often shows up *accidentally* when a join's `ON` clause is missing or a comma-separated `FROM` list forgets a `WHERE` condition.
- Genuinely useful for generating combinations on purpose (sizes × colors, dates × stores).

---

## ⚙️ How It Works

Every row from the first table is combined with every row from the second, with no filtering at all.

```text
Sizes: S, M, L         Colors: Red, Blue
CROSS JOIN → S-Red, S-Blue, M-Red, M-Blue, L-Red, L-Blue
```

---

## 💻 Examples

```sql
SELECT
	s.SizeName,
	c.ColorName
FROM Sizes s
CROSS JOIN Colors c;
```

Generates every size/color combination — useful for building out a full product variant matrix before filtering to what's actually in stock.

---

## 🚀 Real World Applications

- Generating every product variant combination (size × color) before filtering to real inventory
- Building a calendar table (every date × every store location)
- Producing test data or scenario matrices

---

## ⚖️ Advantages

- The simplest way to generate every possible combination between two sets.
- No ambiguity about matching logic — there isn't any.

---

## ⚠️ Limitations

- Result size explodes quickly: two 10,000-row tables produce 100,000,000 rows.
- Almost never useful against two large, unrelated production tables.

---

## 🚨 Common Mistakes

- Writing `FROM TableA, TableB` without a `WHERE` condition and accidentally getting a full Cartesian product instead of a filtered join — this is the classic silent-CROSS-JOIN bug.
- Running one against two large tables and being surprised by a runaway result set or a locked-up query.

---

## 📖 Further Reading

Documentation

Books

Videos

Articles

---

## 💡 Wisdom from Mímir

If a query returns far more rows than expected after joining two tables, check for a missing `ON` condition before anything else — an accidental CROSS JOIN is one of the most common ways a query silently multiplies its result set.

---

## 🔗 Related Notes

- [[INNER JOIN]]
- [[FULL OUTER JOIN]]
