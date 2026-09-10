---
type: query-pattern
status: published
created: 2026-08-12
updated: 2026-08-12
technology: SQL
difficulty: Beginner
tags:
  - order-by
  - limit
  - sorting
  - top-n
aliases:
  - Last 10 Records
  - Most Recent Records
  - Retrieving Recent Rows
publish: true
---

# 🧩 Retrieving the Last N Records

> *Sort records by a column that defines sequence or recency and limit the result to the desired number of rows.*

---

## 🎯 Problem

Return the last `N` records from a table. 

For example: 
> Return the 10 most recently inserted employees.

---

## 🤔 Mental Model

SQL tables do **not** inherently have a meaningful "first" or "last" row. 

You must first define what **last** means. 

For example: 
- Highest ID 
- Latest creation timestamp 
- Most recent transaction date 
- Latest sequence number 

Then sort by that value and take the first `N` rows from the sorted result.

---

## 🧠 Why This Pattern Works

Sorting descending places the newest or highest-valued records first: 

```sql 
ORDER BY employee_id DESC 
``` 

`LIMIT` then restricts the result: 

```sql 
LIMIT 10 
``` 

Together: 
> Sort newest → oldest → keep the first 10.

---

## 💻 SQL Solution

### PostgreSQL / MySQL
```sql
SELECT * 
FROM employees 
ORDER BY employee_id 
DESC LIMIT 10;
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Define "last" 

Here we assume a larger `employee_id` represents a newer employee. 
### Step 2 — Reverse the order 

```sql 
ORDER BY employee_id DESC 
``` 

The highest IDs appear first. 
### Step 3 — Restrict the result 

```sql 
LIMIT 10 
``` 

Only ten rows are returned.

## 🧪 Better Example Using a Timestamp 

When available, an actual creation timestamp is often clearer: 

```sql 
SELECT * 
FROM employees 
ORDER BY created_at DESC 
LIMIT 10; 
``` 

This explicitly asks for the ten most recently created records. 

--- 
## 🧪 SQL Dialect Variations 

### SQL Server 

```sql 
SELECT TOP 10 * 
FROM employees 
ORDER BY employee_id DESC; 
``` 

### PostgreSQL / MySQL 

```sql 
SELECT * 
FROM employees 
ORDER BY employee_id DESC 
LIMIT 10; 
``` 

### Standard-style OFFSET/FETCH 

```sql 
SELECT * 
FROM employees 
ORDER BY employee_id DESC 
FETCH FIRST 10 ROWS ONLY; 
``` 

Support varies by database.


---

## 🚀 Common Use Cases

- Recent transactions 
- Latest log entries 
- Newest customers 
- Recent orders 
- Latest application events 
- Recently created records

---

## ⚖️ Alternatives

If you need records based on actual time rather than insertion sequence, order by a timestamp: 

```sql 
ORDER BY created_at DESC 
``` 

This is usually more expressive than assuming a primary key represents chronology.

---

## ⚠️ Performance Considerations

- Index the sorting column when appropriate. 
- Sorting a large unindexed dataset can be expensive. 
- Avoid `SELECT *` in production when only certain columns are needed. 
- A descending index may benefit frequently executed recent-record queries.

---

## 🚨 Common Mistakes

- Using `LIMIT 10` without `ORDER BY`. 
- Assuming database rows have a natural order. 
- Assuming the highest ID always means the newest record. 
- Sorting ascending instead of descending. 
- Using a column that does not reliably represent recency.

---

## 💡 Wisdom from Mímir

There is no meaningful "last row" until the query defines an order. 

Whenever someone asks for the: 
> "last 10 records" 

my first question should be: 
> **Last according to what?** 

If the requirement means "most recent," prefer a reliable timestamp such as `created_at` over assuming that a primary key represents time. 

---

## 🔗 Related Notes

- [[Retrieving the Top N Highest Values]] — the same ORDER BY + LIMIT shape, ranking by value instead of recency
- [[How to Fetch the First and Last Record from a Table]] — combines "first" and "last" into a single query
- [[Filtering Records by a Recent Date Window]] — a different way to scope "recent" records, using a date cutoff instead of a row limit