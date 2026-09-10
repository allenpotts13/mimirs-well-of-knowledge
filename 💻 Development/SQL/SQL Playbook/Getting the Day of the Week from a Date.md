---
type: query-pattern
status: active
created: 2026-09-02
updated: 2026-09-02
technology: SQL
difficulty: Beginner
tags:
  - query-pattern
  - date-functions
  - dayname
aliases:
  - DAYNAME function
publish: false
---

# <span class="rune">ᚲ</span> Getting the Day of the Week from a Date

> *Turn a stored date into a readable weekday name.*

---

## 🎯 Problem

How do you display the hire date and day of the week for each employee?

---

## 💻 SQL Solution

```sql
SELECT name, hire_date, DAYNAME(hire_date) AS day_of_week
FROM employees;
```

---

## 📝 Notes

- `DAYNAME()` returns the weekday as a string (e.g. "Monday"), useful for display.
- `DAYOFWEEK()` is the related function returning a number instead (1 = Sunday through 7 = Saturday in MySQL) — reach for that when you need to sort or filter by weekday rather than just print it.
- Like `MONTHNAME()`, the returned name reflects the server's locale — see [[Getting the Current Month Name]] for the same caveat.

---

## 🔗 Related Notes

- [[Getting the Current Month Name]]
- [[Displaying the Current Date and Time]]
