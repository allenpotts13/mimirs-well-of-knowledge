---
type: query-pattern
status: published
created: 2026-08-12
updated: 2026-08-12
technology: SQL
difficulty: Beginner
tags:
  - dates
  - filtering
  - year
  - where
aliases:
  - Employees Who Joined in 2020
  - Filtering Dates by Year
publish: true
---

# <span class="rune">ᚲ</span> Filtering Records by Year

> *Return records whose date falls within a specific calendar year.*

---

## 🎯 Problem

Find all employees whose `join_date` falls within the year 2020.

---

## 🤔 Mental Model

A date contains multiple pieces: 

```text 
2020-08-15 
^^^^ 
Year 
``` 

One approach extracts the year and compares it to the desired value.

Another approach defines a date range covering the entire year.

---

## 🧠 Why This Pattern Works

The source image uses: 

```sql 
WHERE YEAR(join_date) = 2020; 
```

That works in databases such as SQL Server and MySQL. 

However, a date-range comparison is often more portable and can be more index-friendly.

---

## 💻 SQL Solution

### SQL Server / MySQL style
```sql
SELECT * 
FROM employees 
WHERE YEAR(join_date) = 2020;
```

---
## 🧪 Preferred Range-Based Pattern

```sql 
SELECT * 
FROM employees 
WHERE join_date >= '2020-01-01' 
	AND join_date < '2021-01-01'; 
``` 

This means: 
> On or after January 1, 2020, but before January 1, 2021.

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Define the lower boundary 

```sql 
join_date >= '2020-01-01' 
``` 

### Step 2 — Define the exclusive upper boundary 

```sql 
join_date < '2021-01-01' 
``` 

### Step 3 — Return everything within that interval

This safely includes every date and timestamp in 2020.

---

## 🚀 Common Use Cases

- Employees hired in a year 
- Orders placed during a year 
- Transactions from a fiscal period 
- Events occurring during a reporting year 
- Historical trend analysis

---

## ⚖️ Alternatives

### PostgreSQL 

```sql 
SELECT * 
FROM employees 
WHERE EXTRACT(YEAR FROM join_date) = 2020; 
``` 

### Date range 

```sql 
WHERE join_date >= '2020-01-01' 
	AND join_date < '2021-01-01' 
``` 

The range version is generally the most portable mental model.

---

## ⚠️ Performance Considerations

Applying a function directly to an indexed column can sometimes make index usage less efficient. 

For example: 
```sql 
YEAR(join_date)
``` 

may require the database to calculate the year for many rows. 

A range predicate can often work better with an index on `join_date`.

---

## 🚨 Common Mistakes

- Using `LIKE '2020%'` on a real date column. 
- Forgetting that timestamps include time-of-day values. 
- Using `<= '2020-12-31'` and accidentally excluding late-day timestamps. 
- Assuming `YEAR()` exists in every SQL dialect. 
- Filtering the wrong date column.

---

## 💡 Wisdom from Mímir

For date filtering, I prefer thinking in **ranges** rather than extracting date parts. 

This: 

```sql 
>= start_date 
AND < next_period_start 
``` 

is predictable, handles timestamps cleanly, and often works well with indexes.

---

## 🔗 Related Notes

- [[Filtering Records by a Recent Date Window]] — the same range-over-extraction principle, applied to a rolling window instead of a fixed calendar year
- [[Aggregating Values Within a Date Range]] — combines this kind of date-range filter with a GROUP BY aggregate
- [[Calculating Age from a Birth Date]] — another date-math pattern worth comparing against extracting date parts