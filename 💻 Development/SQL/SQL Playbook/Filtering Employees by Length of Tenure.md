---
type: query-pattern
status: published
created: 2026-09-02
updated: 2026-09-02
technology: SQL
difficulty: Beginner
tags:
  - date-functions
  - datediff
  - filtering
  - tenure
aliases:
  - Employees With More Than 5 Years of Service
  - DATEDIFF Filtering
publish: true
---

# 🧩 Filtering Employees by Length of Tenure

> *Use `DATEDIFF()` to measure the number of days between a stored date and today, then filter on that duration.*

---

## 🎯 Problem

Display all employees who have been in the company for more than 5 years.

More generally, this pattern answers:
> How do I filter rows based on how much time has elapsed since a stored date?

---

## 🤔 Mental Model

Picture standing at today's date and stretching a tape measure backward to each employee's join date. `DATEDIFF()` reads the number on the tape in days. Anything longer than your cutoff mark passes the filter.

---

## 🧠 Why This Pattern Works

`DATEDIFF(date1, date2)` returns the number of days between two dates as a plain integer. By anchoring one side to `CURDATE()` (today's date with no time component) and the other side to a stored column, the expression becomes a moving target that recalculates correctly every time the query runs — you never have to hardcode "5 years ago" as a fixed date that goes stale tomorrow.

The comparison then becomes ordinary arithmetic: convert the "5 years" business rule into an approximate day count (`5 × 365 = 1825`) and compare with `>`.

---

## 💻 SQL Solution

```sql
SELECT *
FROM employees
WHERE DATEDIFF(CURDATE(), join_date) > 1825;
```

Ordering `DATEDIFF(CURDATE(), join_date)` — today minus the join date — keeps the result positive for anyone who has already joined; reversing the arguments would flip the sign.

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Get today's date

```sql
CURDATE()
```

MySQL evaluates this once per query execution, returning the current date with no time portion.

### Step 2 — Compute the day difference per row

```sql
DATEDIFF(CURDATE(), join_date)
```

For each employee, MySQL subtracts `join_date` from today and returns the number of whole days elapsed.

### Step 3 — Apply the threshold

```sql
WHERE DATEDIFF(CURDATE(), join_date) > 1825
```

Only rows where the elapsed day count exceeds 1825 (roughly 5 years) survive the filter.

### Step 4 — Return the matching rows

```sql
SELECT *
```

Every column for each surviving employee is returned.

---

## 🧪 Example Data

Assume today is `2026-09-02`.

| employee_id | name  | join_date  |
| ----------- | ----- | ---------- |
| 1           | Alice | 2018-01-15 |
| 2           | Bob   | 2023-06-01 |
| 3           | Carol | 2020-03-10 |

Query:
```sql
SELECT *
FROM employees
WHERE DATEDIFF(CURDATE(), join_date) > 1825;
```

Result:

| employee_id | name  | join_date  |
| ----------- | ----- | ---------- |
| 1           | Alice | 2018-01-15 |
| 3           | Carol | 2020-03-10 |

Bob's tenure (roughly 3 years and 3 months as of the example date) falls short of 1825 days, so he is excluded.

---

## 🚀 Common Use Cases

- Identifying employees eligible for a tenure-based bonus or long-service award
- Flagging accounts, subscriptions, or licenses older than a retention policy allows
- Finding customers who have been active for longer than a loyalty-tier threshold
- Surfacing overdue invoices or tickets open longer than an SLA window
- Segmenting users by "new" vs. "veteran" cohorts for reporting

---

## ⚖️ Alternatives

### Deleting instead of selecting (same pattern, destructive action)

A near-identical business question sometimes asks for cleanup rather than a report — for example, "delete employees who have been in the company for more than 15 years" (perhaps because they have since moved to an archive table):

```sql
DELETE FROM employees
WHERE DATEDIFF(CURDATE(), join_date) > 5475;
```

This is the exact same `DATEDIFF()` filter, just swapped from `SELECT *` to `DELETE FROM`, with the threshold recalculated for 15 years (`15 × 365 = 5475` days). Because `DELETE` is destructive, it's good practice to first run the equivalent `SELECT *` with the same `WHERE` clause to confirm the row set before deleting it.

### Using `TIMESTAMPDIFF()` for calendar-accurate years

```sql
SELECT *
FROM employees
WHERE TIMESTAMPDIFF(YEAR, join_date, CURDATE()) > 5;
```

`TIMESTAMPDIFF(YEAR, ...)` counts complete calendar years rather than approximating with a fixed day count, so it correctly handles leap years and doesn't drift the way a hardcoded "365 days per year" multiplier can over long tenures.

### Comparing against a computed cutoff date

```sql
SELECT *
FROM employees
WHERE join_date < DATE_SUB(CURDATE(), INTERVAL 5 YEAR);
```

Instead of measuring elapsed days, this computes the cutoff date once ("5 years ago from today") and compares `join_date` directly against it — often easier to read and just as accurate as `TIMESTAMPDIFF()`.

---

## ⚠️ Performance Considerations

- `WHERE DATEDIFF(CURDATE(), join_date) > 1825` wraps `join_date` in a function, which prevents MySQL from using a standard index on `join_date` (the index becomes non-sargable).
- The `DATE_SUB()` cutoff-date alternative keeps `join_date` bare on one side of the comparison, allowing an index on `join_date` to be used efficiently.
- On small to medium employee tables the difference is negligible; on large historical or event tables, prefer the sargable form.
- `CURDATE()` is evaluated once per statement, not once per row, so it does not add meaningful overhead itself.

---

## 🚨 Common Mistakes

- Reversing the arguments to `DATEDIFF(join_date, CURDATE())`, which produces a negative number and breaks the `>` comparison.
- Assuming 365 days per year exactly; over long spans this drifts from calendar reality (leap years add up).
- Wrapping the date column in a function and then being surprised the query is slow on a large table.
- Forgetting that `join_date` might be `NULL` for some rows — `DATEDIFF()` against `NULL` returns `NULL`, silently excluding those rows from both `>` and `<` comparisons.
- Confusing "more than 5 years" with "at least 5 years" — `>` versus `>=` changes whether employees at the exact boundary are included.

---

## 💡 Wisdom from Mímir

A tenure filter is really asking a question about the future disguised as a question about the past:
> "Who has already crossed the line I'm about to draw?"

The line itself — 5 years, 15 years, 90 days — is a business decision, not a SQL decision. Once you've decided where the line sits, the query is just measuring distance from today back to a fixed point in time.

---

## 🔗 Related Notes

- [[Filtering Records by Year]]
- [[Displaying the Current Date and Time]]
- [[Delete All Employees from a Department]]
- [[Filtering Records by a Recent Date Window]]
- [[Calculating Age from a Birth Date]]
