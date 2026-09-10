---
type: query-pattern
status: published
created: 2026-09-02
updated: 2026-09-02
technology: SQL
difficulty: Beginner
tags:
  - date-functions
  - date_sub
  - interval
  - filtering
aliases:
  - Employees Hired in the Last 6 Months
  - DATE_SUB Filtering
publish: true
---

# 🧩 Filtering Records by a Recent Date Window

> *Use `DATE_SUB()` with `CURDATE()` to compute a calendar-accurate cutoff date, then filter rows that fall after it.*

---

## 🎯 Problem

List all employees hired in the last 6 months.

More generally, this pattern answers:
> How do I filter rows whose date falls within a trailing window of time ending today?

---

## 🤔 Mental Model

Picture a window that slides along the calendar, always ending on today and always stretching back a fixed distance — six months, thirty days, a year. Any row whose date falls inside that window passes the filter; anything older is left behind as the window slides forward each day.

---

## 🧠 Why This Pattern Works

`DATE_SUB(CURDATE(), INTERVAL 6 MONTH)` asks MySQL to subtract a calendar interval — not a fixed number of days — from today's date. MySQL understands "6 months" in terms of the calendar itself, correctly handling different month lengths and leap years, and returns a single concrete cutoff date.

Once that cutoff date exists, the filter is just an ordinary comparison: any `hire_date` on or after the cutoff falls inside the trailing window.

---

## 💻 SQL Solution

```sql
SELECT *
FROM employees
WHERE hire_date >= DATE_SUB(CURDATE(), INTERVAL 6 MONTH);
```

`CURDATE()` and the `DATE_SUB()` call are evaluated once per query execution, not once per row, so the window automatically slides forward every time the query runs — no hardcoded date to update by hand.

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Get today's date

```sql
CURDATE()
```

MySQL returns today's date with no time component.

### Step 2 — Compute the cutoff date

```sql
DATE_SUB(CURDATE(), INTERVAL 6 MONTH)
```

MySQL subtracts six calendar months from today, producing a single fixed date — the earliest date the window still allows.

### Step 3 — Apply the filter

```sql
WHERE hire_date >= DATE_SUB(CURDATE(), INTERVAL 6 MONTH)
```

Each row's `hire_date` is compared against the cutoff; rows on or after it pass.

### Step 4 — Return the matching rows

```sql
SELECT *
```

Every column for each employee hired within the last six months is returned.

---

## 🧪 Example Data

Assume today is `2026-09-02`, making the cutoff `2026-03-02`.

| employee_id | name  | hire_date  |
| ----------- | ----- | ---------- |
| 1           | Alice | 2026-07-15 |
| 2           | Bob   | 2025-11-01 |
| 3           | Carol | 2026-04-20 |

Query:
```sql
SELECT *
FROM employees
WHERE hire_date >= DATE_SUB(CURDATE(), INTERVAL 6 MONTH);
```

Result:

| employee_id | name  | hire_date  |
| ----------- | ----- | ---------- |
| 1           | Alice | 2026-07-15 |
| 3           | Carol | 2026-04-20 |

Bob's hire date falls before the `2026-03-02` cutoff, so he is excluded.

---

## 🚀 Common Use Cases

- Onboarding reports for employees hired within a recent window
- New-customer or new-signup dashboards over a trailing period
- Recent-orders or recent-transactions views for operational reporting
- Sync jobs that only need records touched within the last N days
- Trailing-window churn or activity analysis (e.g., "active in the last 90 days")
- Highlighting recently updated tickets, invoices, or support cases

---

## ⚖️ Alternatives

### DATEDIFF-based tenure filtering

```sql
SELECT *
FROM employees
WHERE DATEDIFF(CURDATE(), hire_date) <= 180;
```

This computes the same rough window using a fixed day count instead of a calendar interval. It works, but "180 days" and "6 months" aren't exactly the same length depending on which months are involved, so the boundary can drift over time. See [[Filtering Employees by Length of Tenure]] for the DATEDIFF-based version of this pattern applied to a much longer tenure window, where that drift becomes more noticeable.

### Bounded window with explicit start and end dates

```sql
SELECT *
FROM employees
WHERE hire_date BETWEEN '2026-01-01' AND '2026-06-30';
```

Useful when the window isn't trailing to today but instead refers to a fixed historical period, such as "hired during the first half of 2026." Unlike the `DATE_SUB()` version, this window doesn't move as time passes.

---

## ⚠️ Performance Considerations

- Because `hire_date` is compared directly against a computed constant, this filter is sargable — MySQL can use a standard index on `hire_date` efficiently, unlike wrapping the column itself in a function such as `DATEDIFF(CURDATE(), hire_date)`.
- `CURDATE()` and `DATE_SUB()` are each evaluated once per statement, not once per row, so they add negligible overhead.
- On large historical tables, an index on the date column makes trailing-window queries fast even as the table grows, since only recent rows need to be scanned.
- If the column stores a `DATETIME` with a time component, comparing against `CURDATE()` (which has no time portion) can produce subtly different boundary results than comparing against `NOW()`.

---

## 🚨 Common Mistakes

- Hardcoding a literal cutoff date instead of computing it with `DATE_SUB(CURDATE(), ...)`, which silently goes stale the day after it's written.
- Using `DATEDIFF()` with an approximated day count (e.g., `180` for "6 months") when a calendar-accurate `INTERVAL` would be more correct.
- Confusing `>=` with `>` at the boundary, changing whether a row exactly at the cutoff date is included.
- Forgetting that `NULL` values in the date column are excluded from both sides of the comparison.
- Mixing up the direction of the subtraction and computing a future date instead of a past one.

---

## 💡 Wisdom from Mímir

"Recent" is not a fixed point — it's a moving target measured from wherever "now" happens to be.

> A hardcoded date answers yesterday's question. A recomputed window keeps answering today's.

---

## 🔗 Related Notes

- [[Filtering Employees by Length of Tenure]]
- [[Filtering Records by Year]]
- [[Displaying the Current Date and Time]]
- [[Aggregating Values Within a Date Range]]
- [[Calculating Age from a Birth Date]]
