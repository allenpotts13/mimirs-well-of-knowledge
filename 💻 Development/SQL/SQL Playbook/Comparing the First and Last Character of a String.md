---
type: query-pattern
status: active
created: 2026-09-02
updated: 2026-09-02
technology: SQL
difficulty: Beginner
tags:
  - query-pattern
  - string-functions
  - left
  - right
aliases:
  - LEFT and RIGHT string functions
---

# 🧩 Comparing the First and Last Character of a String

> *Pull single characters off either end of a string with LEFT() and RIGHT() to compare them.*

---

## 🎯 Problem

How do you count employees whose names start and end with the same letter?

---

## 💻 SQL Solution

```sql
SELECT COUNT(*) FROM employees WHERE LEFT(name, 1) = RIGHT(name, 1);
```

---

## 📝 Notes

- `LEFT(str, n)` returns the first `n` characters of a string; `RIGHT(str, n)` returns the last `n` characters. Passing `1` extracts a single character from either end.
- Whether `'Anna'` and `'anna'` count as matching depends on the column's collation — a case-sensitive collation treats `A` and `a` as different characters, while a case-insensitive one treats them as equal. Wrap both sides in `LOWER()` (see [[Converting Text to Lowercase]]) if you want the comparison to always ignore case.

---

## 🔗 Related Notes

- [[Converting Text to Lowercase]]
- [[Matching Text Patterns with LIKE]]
