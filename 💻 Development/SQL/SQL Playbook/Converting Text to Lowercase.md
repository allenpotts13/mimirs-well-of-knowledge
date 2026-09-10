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
  - lower
aliases:
  - LOWER function
publish: false
---

# 🧩 Converting Text to Lowercase

> *Normalize a string to all lowercase characters.*

---

## 🎯 Problem

How do you convert a string value to lowercase?

---

## 💻 SQL Solution

```sql
SELECT LOWER('STRING_VALUE');
```

---

## 📝 Notes

- `UPPER()` is the counterpart, converting a string to all-caps.
- A very common real use is case-insensitive comparison — e.g. `WHERE LOWER(email) = LOWER(?)` — when a column's collation is case-sensitive and you can't guarantee consistent input casing.
- Applying `LOWER()` to a column in a `WHERE` clause prevents a plain index on that column from being used efficiently, since the value being compared is computed per row rather than stored. A case-insensitive collation on the column avoids this trade-off entirely if it's an option.

---

## 🔗 Related Notes

- [[Filtering Rows by Email Domain]]
