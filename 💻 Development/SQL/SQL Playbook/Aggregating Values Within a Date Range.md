---
type: query-pattern
status: published
created: 2026-09-02
updated: 2026-09-02
technology: SQL
difficulty: Intermediate
tags:
  - date-functions
  - aggregate-functions
  - grouping
  - reporting
aliases:
  - Year-to-Date Sales
  - Rolling Window Aggregation
  - YTD Calculation
publish: true
---

# <span class="rune">ᚲ</span> Aggregating Values Within a Date Range

> *Combine a `WHERE`-clause date filter with a `GROUP BY` aggregate to summarize activity within a specific window of time, such as year-to-date.*

---

## 🎯 Problem

Retrieve the year-to-date sales for each customer.

More generally, this pattern answers:
> What is the total (or average, or count) of a measure, restricted to a specific window of time, broken down per group?

---

## 🤔 Mental Model

Picture a ledger book where every sale is written on the page for the day it happened. To get "year-to-date sales," you don't read the whole book — you dog-ear the page for January 1st of this year, flip to today, and only add up the entries between those two dog-ears. `WHERE sale_date BETWEEN ...` is placing those dog-ears; `GROUP BY customer_id` is sorting the entries between them into piles by customer before adding each pile up.

---

## 🧠 Why This Pattern Works

`WHERE` filters *which rows* are eligible for aggregation before any grouping happens — here, only sales between January 1st and today. `GROUP BY customer_id` then partitions the surviving rows into per-customer buckets, and `SUM(sales_amount)` totals each bucket independently. The order matters conceptually: filtering happens first (narrowing the time window), then grouping and aggregating happens second (summarizing within that window, per customer). This two-step "filter, then aggregate" shape is what makes any "totals within a window" question — year-to-date, month-to-date, trailing 30 days — expressible as one query.

---

## 💻 SQL Solution

```sql
SELECT customer_id, SUM(sales_amount) AS ytd_sales
FROM sales
WHERE sale_date BETWEEN '2024-01-01' AND CURRENT_DATE()
GROUP BY customer_id;
```

`CURRENT_DATE()` keeps the end of the window moving with "today," but the hardcoded start date does not — see Common Mistakes below.

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Filter rows to the date window

```sql
WHERE sale_date BETWEEN '2024-01-01' AND CURRENT_DATE()
```

Keeps only sales that happened on or after January 1st, 2024, and on or before today.

### Step 2 — Group the remaining rows by customer

```sql
GROUP BY customer_id
```

Collapses the filtered sales into one bucket per customer.

### Step 3 — Sum each customer's sales within the window

```sql
SUM(sales_amount) AS ytd_sales
```

Adds up the `sales_amount` values within each customer's bucket, producing their year-to-date total.

---

## 🧪 Example Data

Assume today's date is 2026-09-02, and the query's hardcoded start date has been updated to `'2026-01-01'` for this example.

| customer_id | sale_date  | sales_amount |
| ----------: | ---------- | -----------: |
|           1 | 2025-11-20 |           500 |
|           1 | 2026-02-14 |           300 |
|           1 | 2026-07-01 |           200 |
|           2 | 2026-03-10 |           400 |

Query:
```sql
SELECT customer_id, SUM(sales_amount) AS ytd_sales
FROM sales
WHERE sale_date BETWEEN '2026-01-01' AND CURRENT_DATE()
GROUP BY customer_id;
```

The 2025-11-20 sale falls outside the window and is excluded.

Result:

| customer_id | ytd_sales |
| ----------: | --------: |
|           1 |       500 |
|           2 |       400 |

---

## 🚀 Common Use Cases

- Year-to-date sales or revenue per customer
- Month-to-date active users per account
- Quarter-to-date expenses per department
- Trailing-30-days transaction totals per merchant
- Fiscal-year bookings per sales rep
- Season-to-date attendance per venue

---

## ⚖️ Alternatives

### Dynamically computed start of year (more robust)

```sql
SELECT customer_id, SUM(sales_amount) AS ytd_sales
FROM sales
WHERE sale_date BETWEEN MAKEDATE(YEAR(CURDATE()), 1) AND CURRENT_DATE()
GROUP BY customer_id;
```

`MAKEDATE(YEAR(CURDATE()), 1)` builds January 1st of the *current* year on the fly — the query keeps meaning "year-to-date" correctly every year, instead of being frozen to 2024.

### DATE_FORMAT approach to the same dynamic start date

```sql
SELECT customer_id, SUM(sales_amount) AS ytd_sales
FROM sales
WHERE sale_date >= DATE_FORMAT(CURDATE(), '%Y-01-01')
  AND sale_date <= CURRENT_DATE()
GROUP BY customer_id;
```

`DATE_FORMAT(CURDATE(), '%Y-01-01')` string-formats the current year into a January 1st date. Functionally equivalent to `MAKEDATE`, just a different route to the same dynamic boundary.

### Rolling trailing window instead of calendar year-to-date

```sql
SELECT customer_id, SUM(sales_amount) AS last_30_days_sales
FROM sales
WHERE sale_date >= CURRENT_DATE() - INTERVAL 30 DAY
GROUP BY customer_id;
```

Rather than anchoring to a fixed calendar boundary (start of year), this anchors relative to today, producing a rolling window that always covers "the most recent 30 days" — a different but closely related aggregation-within-a-range pattern.

---

## ⚠️ Performance Considerations

- An index on `sale_date` (ideally a composite index on `(customer_id, sale_date)`) lets the database narrow the date range quickly before grouping, rather than scanning the entire `sales` table.
- `BETWEEN` on a date range is generally sargable (index-friendly) in MySQL, but wrapping `sale_date` in a function (e.g., `YEAR(sale_date) = 2026`) prevents index usage — always filter on the raw column against computed boundary values instead.
- For very large sales tables, consider partitioning by date range, which lets the database skip entire partitions outside the query's window.
- Recomputing `CURRENT_DATE()` and window boundaries on every query execution is cheap, but if this query runs extremely frequently, a materialized daily or weekly summary table can avoid repeated full aggregation.

---

## 🚨 Common Mistakes

- **Hardcoding the start date** (`'2024-01-01'`) — this query will silently keep using 2024 as "the start of the year" forever, even in 2027, unless someone remembers to update it by hand every January. A robust version computes the start of the current year dynamically instead.
- Using `<` instead of `<=` (or vice versa) at a date boundary, accidentally excluding or double-counting activity that happens exactly on the boundary date.
- Applying a function to the date column in the `WHERE` clause (e.g., `YEAR(sale_date) = YEAR(CURDATE())`), which can prevent the database from using an index on `sale_date`.
- Forgetting that `BETWEEN` on a `DATE` column with time-inclusive boundaries can silently exclude same-day rows if `sale_date` is actually a `DATETIME` with a nonzero time component — a row timestamped `2026-09-02 14:30:00` is *not* `<= '2026-09-02'` unless compared carefully.
- Grouping by `customer_id` without also filtering out customers with no sales at all in the window — such customers simply won't appear in the result, which is easy to mistake for "zero sales" being explicitly reported rather than the row being absent entirely.

---

## 💡 Wisdom from Mímir

A "year-to-date" query is really two separate promises bundled into one line of SQL: a promise about *where the window starts*, and a promise about *where it ends*. `CURRENT_DATE()` keeps its promise automatically, every single day. A hardcoded `'2024-01-01'` keeps its promise exactly once, then quietly breaks it forever after.

> If one boundary of your window moves with time and the other doesn't, ask yourself: **is that asymmetry intentional, or is it a bug waiting for next January?**

---

## 🔗 Related Notes

- [[Calculating a Total with SUM()]]
- [[Filtering Records by a Recent Date Window]]
- [[Filtering Records by Year]]
- [[Filtering Employees by Length of Tenure]]
- [[Calculating Each Group's Percentage of the Total]]
