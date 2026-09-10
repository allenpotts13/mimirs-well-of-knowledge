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
  - monthname
aliases:
  - MONTHNAME function
---

# 🧩 Getting the Current Month Name

> *Read today's month as a word instead of a number.*

---

## 🎯 Problem

How do you get the current month's name (e.g. "September") rather than its numeric value?

---

## 💻 SQL Solution

```sql
SELECT MONTHNAME(CURDATE());
```

---

## 📝 Notes

- `MONTHNAME()` returns the name in the server's configured locale, so "September" on an English-locale server might come back differently on a server configured for another language.
- `CURDATE()` supplies today's date; swap in any date/datetime column or expression to get that row's month name instead.
- If you need the month as a two-digit number for sorting or grouping, use `MONTH(CURDATE())` or `DATE_FORMAT(CURDATE(), '%m')` instead.

---

## 🔗 Related Notes

- [[Displaying the Current Date and Time]]
- [[Getting the Day of the Week from a Date]]
