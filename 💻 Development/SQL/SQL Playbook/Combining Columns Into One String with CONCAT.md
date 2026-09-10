---
type: query-pattern
status: published
created: 2026-09-02
updated: 2026-09-02
technology: SQL
difficulty: Beginner
tags:
  - query-pattern
  - string-functions
  - concat
aliases:
  - CONCAT function
  - Building a formatted string column
publish: true
permalink: sql/combining-columns-into-one-string-with-concat
---

# <span class="rune">ᚲ</span> Combining Columns Into One String with CONCAT

> *Stitch multiple columns and literal text together into one readable string.*

---

## 🎯 Problem

How do you retrieve employee names and salaries combined into a single display string?

---

## 💻 SQL Solution

```sql
SELECT CONCAT(name, ' earns ', salary) AS employee_info
FROM employees;

-- With a formatted date included
SELECT CONCAT(name, ' - ', DATE_FORMAT(hire_date, '%m/%d/%Y')) AS employee_info
FROM employees;
```

---

## 📝 Notes

- In MySQL, if *any* argument to `CONCAT()` is `NULL`, the entire result is `NULL` — a single missing value can silently blank out the whole string. Use `CONCAT_WS(separator, ...)` instead if you want NULL arguments skipped rather than poisoning the result.
- `DATE_FORMAT()` format specifiers like `%m` (2-digit month), `%d` (2-digit day), and `%Y` (4-digit year) are MySQL-specific — other databases use different formatting functions/tokens.
- Handy for building readable labels, CSV-style exports, or log messages directly from a query.

---

## 🔗 Related Notes

- [[Getting the Current Month Name]]
- [[Exporting Query Results as XML]]
