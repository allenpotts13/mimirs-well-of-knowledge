---
type: query-pattern
status: published
created: 2026-09-02
updated: 2026-09-02
technology: SQL
difficulty: Beginner
tags:
  - query-pattern
  - like
  - wildcards
  - string-matching
aliases:
  - Ends-with pattern matching
publish: true
---

# <span class="rune">ᚲ</span> Matching Text Patterns with LIKE

> *Match text by shape using LIKE's wildcard characters.*

---

## 🎯 Problem

How do you fetch all employees whose names end with the letter "n"?

---

## 💻 SQL Solution

```sql
SELECT * FROM employees WHERE name LIKE '%n';
```

---

## 📝 Notes

- `%` matches any number of characters (including zero); `_` matches exactly one character. `'%n'` means "anything, then a literal n at the end."
- Leading-wildcard patterns like `'%n'` can't use a standard B-tree index efficiently — the database can't jump to a starting point, so it forces a full table scan. A trailing-wildcard pattern like `'n%'` (starts-with) can still use an index, since matches share a common prefix.
- This is the mirror image of a starts-with search — see [[Finding Values That Start With a Pattern]] for the `'n%'` version.

---

## 🔗 Related Notes

- [[Finding Values That Start With a Pattern]]
- [[Filtering Rows by Email Domain]]
