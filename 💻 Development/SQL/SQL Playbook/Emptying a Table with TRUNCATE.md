---
type: query-pattern
status: active
created: 2026-09-02
updated: 2026-09-02
technology: SQL
difficulty: Beginner
tags:
  - query-pattern
  - truncate
  - data-cleaning
aliases:
  - Removing all rows from a table
---

# 🧩 Emptying a Table with TRUNCATE

> *Remove every row from a table in one fast, unlogged sweep while keeping the table structure intact.*

---

## 🎯 Problem

How do you remove all records from a table but keep the table itself (columns, indexes, constraints) for future use?

---

## 💻 SQL Solution

```sql
TRUNCATE TABLE table_name;
```

---

## 📝 Notes

- `TRUNCATE` deallocates the data pages rather than deleting rows one at a time, so it's dramatically faster than `DELETE` on large tables.
- It resets `AUTO_INCREMENT` counters back to their starting value, unlike `DELETE`.
- It cannot take a `WHERE` clause — it's all-or-nothing. If you need to remove only some rows, use `DELETE ... WHERE` instead (see [[Delete All Employees from a Department]]).
- In MySQL/InnoDB, `TRUNCATE` is not fully transactional the way `DELETE` is — it implicitly commits and generally can't be rolled back mid-transaction.
- It also fails (or requires special handling) if foreign keys reference the table, since it bypasses row-by-row constraint checks.

---

## 🔗 Related Notes

- [[Delete All Employees from a Department]]
- [[Checking Whether a Table Is Empty]]
