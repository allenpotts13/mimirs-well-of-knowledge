---
type: concept
status: published
created: 2026-09-10
updated: 2026-09-10
technology: SQL
difficulty: Beginner
tags:
  - set-operations
  - query-structure
aliases:
  - UNION
  - UNION ALL
publish: true
---

# <span class="rune">ᛟ</span> UNION vs UNION ALL

> Both stack the results of two queries on top of each other — UNION removes duplicates in the process, UNION ALL doesn't.

---

## 🎯 Purpose

`UNION` and `UNION ALL` combine the result rows of two or more `SELECT` statements into a single result set, as long as each query returns the same number of columns with compatible types.

Use `UNION ALL` by default; reach for `UNION` only when duplicates genuinely need to be removed.

---

## 🧠 Key Ideas

- Both queries being combined must return the same number of columns, in compatible data types.
- `UNION` removes duplicate rows across the combined result — this means it has to sort/compare every row.
- `UNION ALL` keeps every row, duplicates included, and skips that comparison step entirely.
- `UNION ALL` is always at least as fast as `UNION`, often significantly faster on large result sets.

---

## ⚙️ How It Works

```text
UNION ALL:  stack every row from both queries, no deduplication
UNION:      stack every row, then remove exact duplicate rows
```

---

## 💻 Examples

```sql
-- Combine active and archived customers, keeping duplicates if any
SELECT CustomerName FROM ActiveCustomers
UNION ALL
SELECT CustomerName FROM ArchivedCustomers;

-- Same, but remove any customer name appearing in both
SELECT CustomerName FROM ActiveCustomers
UNION
SELECT CustomerName FROM ArchivedCustomers;
```

---

## 🚀 Real World Applications

- Combining results from two similar tables (current year + archive)
- Merging results from two different queries against the same table under different conditions
- Simulating a [[FULL OUTER JOIN]] on engines without native support

---

## ⚖️ Advantages

- A simple, readable way to stack multiple result sets into one.
- `UNION ALL` is cheap — no extra sorting or comparison work.

---

## ⚠️ Limitations

- Column count and types must line up between every query being combined.
- `UNION` can be expensive on large result sets, since deduplication requires comparing every row.

---

## 🚨 Common Mistakes

- Using `UNION` out of habit when the queries can't produce duplicates anyway (e.g. combining two tables with non-overlapping ID ranges) — `UNION ALL` would be identical and faster.
- Forgetting that column order determines matching, not column names — mismatched columns silently combine the wrong data instead of erroring.
- Expecting `ORDER BY` to apply to each individual query instead of the combined result — it only works once, at the very end.

---

## 📖 Further Reading

Documentation

Books

Videos

Articles

---

## 💡 Wisdom from Mímir

Default to `UNION ALL`. Reach for `UNION` only when duplicates are actually possible and actually unwanted — otherwise it's paying for a deduplication step that never finds anything to deduplicate.

---

## 🔗 Related Notes

- [[FULL OUTER JOIN]]
- [[Finding Common Records Between Two Tables]]
