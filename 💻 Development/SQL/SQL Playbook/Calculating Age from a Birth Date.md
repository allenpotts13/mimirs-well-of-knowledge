---
type: query-pattern
status: published
created: 2026-09-02
updated: 2026-09-02
technology: SQL
difficulty: Beginner
tags:
  - query-pattern
  - date-functions
  - datediff
  - timestampdiff
aliases:
  - Filtering by age
publish: true
---

# <span class="rune">ᚲ</span> Calculating Age from a Birth Date

> *Derive an age filter from a birth date column — and watch out for the naive version's drift.*

---

## 🎯 Problem

How do you find all employees who are older than 30 years?

---

## 💻 SQL Solution

```sql
SELECT * FROM employees WHERE DATEDIFF(CURDATE(), birth_date) / 365 > 30;
```

---

## 📝 Notes

**Gotcha:** dividing the day count by a flat `365` ignores leap years, so the result slowly drifts inaccurate the further back the birth date is — over decades this can misclassify someone right around the 30-year boundary.

The more correct MySQL approach accounts for actual calendar years:

```sql
SELECT * FROM employees WHERE TIMESTAMPDIFF(YEAR, birth_date, CURDATE()) > 30;
```

- `TIMESTAMPDIFF(YEAR, ...)` correctly handles leap years and calendar boundaries, so prefer it for real age calculations.
- This is the same `DATEDIFF`-based filtering family as [[Filtering Employees by Length of Tenure]], just applied to `birth_date` instead of `hire_date`.

---

## 🔗 Related Notes

- [[Filtering Employees by Length of Tenure]]
- [[Displaying the Current Date and Time]]
