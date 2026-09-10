---
type: query-pattern
status: published
created: 2026-09-02
updated: 2026-09-02
technology: SQL
difficulty: Beginner
tags:
  - query-pattern
  - modulo
  - where
aliases:
  - Filtering by divisibility
publish: true
permalink: sql/filtering-values-that-are-a-multiple-of-a-number
---

# <span class="rune">ᚲ</span> Filtering Values That Are a Multiple of a Number

> *Use the modulo operator to test whether a numeric column divides evenly.*

---

## 🎯 Problem

How do you fetch employees whose salary is an exact multiple of 10,000?

---

## 💻 SQL Solution

```sql
SELECT * FROM employees WHERE salary % 10000 = 0;
```

---

## 📝 Notes

- `%` is the modulo operator — it returns the remainder of dividing the left value by the right value. A remainder of `0` means the number divides evenly, i.e. it's a multiple.
- This is a computed expression evaluated per row, so a standard index on `salary` can't be used to satisfy it directly — expect a full table scan unless your database supports a functional/expression index built on `salary % 10000`.
- Same technique works for any "every Nth" or "divisible by" filter, not just salaries.

---

## 🔗 Related Notes

- [[Finding Values Within a Range]]
