---
type: query-pattern
status: published
created: 2026-08-12
updated: 2026-08-12
technology: SQL
difficulty: Beginner
tags:
  - top-n
  - order-by
  - limit
  - ranking
aliases:
  - Top 5 Employees by Salary
  - Highest Salaries
  - Top N Records
publish: true
---

# 🧩 Retrieving the Top N Highest Values

> *Sort records from highest to lowest by a target value and return only the first N rows.*

---

## 🎯 Problem

Return the five employees with the highest salaries. 

More generally: 
> Return the top `N` records according to a measurable value.

---

## 🤔 Mental Model

Imagine a leaderboard. 

First, rank everyone from highest score to lowest: 

```text 
1. 150,000 
2. 145,000 
3. 140,000
4. 135,000 
5. 130,000 
6. 125,000 
``` 

Then stop after position five.

---

## 🧠 Why This Pattern Works

`ORDER BY salary DESC` sorts the highest salary first. 

`LIMIT 5` restricts the sorted result to five rows. 

The order matters: 
> Rank the records first, then take the requested number.

---

## 💻 SQL Solution

### PostgreSQL / MySQL
```sql
SELECT 
	employee_id, 
	employee_name, 
	salary 
FROM employees 
ORDER BY salary DESC 
LIMIT 5;
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Choose the ranking value

```sql 
salary 
``` 
### Step 2 — Sort highest to lowest

```sql 
ORDER BY salary DESC 
``` 
### Step 3 — Restrict the result 

```sql 
LIMIT 5 
``` 

Only the five highest-ranked rows remain.

## 🧪 SQL Dialect Variations 

### SQL Server 

```sql 
SELECT TOP 5 
	employee_id, 
	employee_name, 
	salary 
FROM employees 
ORDER BY salary DESC; 
``` 

### PostgreSQL / MySQL 

```sql 
SELECT 
	employee_id, 
	employee_name, 
	salary 
FROM employees 
ORDER BY salary DESC 
LIMIT 5; 
```

---

## 🚀 Common Use Cases

- Highest-paid employees 
- Top-selling products 
- Highest-value customers 
- Best-performing locations 
- Highest-scoring students 
- Top-performing sales representatives

---

## ⚖️ Alternatives

### DENSE_RANK() 

If ties should share a ranking: 

```sql 
SELECT 
	employee_id, 
	employee_name, 
	salary 
FROM 
( 
	SELECT 
		employee_id, 
		employee_name, 
		salary, 
		DENSE_RANK() OVER ( 
			ORDER BY salary DESC 
		) AS salary_rank 
	FROM employees 
) ranked 
WHERE salary_rank <= 5; 
``` 

This answers a subtly different question. 

`LIMIT 5` means: 
> Give me exactly five rows. 

`DENSE_RANK() <= 5` means: 
> Give me everyone belonging to the five highest distinct salary levels. 

That could return more than five employees.

---

## ⚠️ Performance Considerations

- Indexing the ranking column may improve performance. 
- Large sorts can be expensive. 
- Select only required columns. 
- Consider how ties should be handled before choosing the implementation.

---

## 🚨 Common Mistakes

- Using `LIMIT` without `ORDER BY`. 
- Sorting with `ASC` instead of `DESC`. 
- Assuming Top 5 rows and Top 5 distinct values mean the same thing. 
- Ignoring ties at the cutoff. 
- Using `SELECT *` unnecessarily. 
- Forgetting that `LIMIT` is not supported by every SQL dialect.

---

## 💡 Wisdom from Mímir

Before writing a Top-N query, clarify what happens when values tie. 

Suppose salaries are: 

```text 
150000 
145000 
140000 
135000 
130000 
130000 
```

Should "Top 5" return exactly five employees? 

Or should both employees earning `130000` be included? 

That business requirement determines whether a simple row limit or a ranking function is the correct solution.

---

## 🔗 Related Notes

- [[Finding the Nth Highest Value]] — for the Nth distinct value specifically, rather than the top N rows
- [[Ranking the Top N Rows Within Each Group]] — the same DENSE_RANK-for-ties idea mentioned in this note's Alternatives, applied per group
- [[Retrieving the Last N Records]] — the same ORDER BY + LIMIT shape, ranking by recency instead of value