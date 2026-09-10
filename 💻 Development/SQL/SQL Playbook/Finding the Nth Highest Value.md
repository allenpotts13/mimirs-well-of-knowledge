---
type: query-pattern
status: published
created: 2026-08-02
updated: 2026-08-02
technology: SQL
difficulty: Intermediate
tags:
  - ranking
  - window-functions
  - dense-rank
  - salary
aliases:
  - Nth Highest Salary
  - Nth Largest Value
publish: true
permalink: sql/finding-the-nth-highest-value
---

# <span class="rune">ᚲ</span> Finding the Nth Highest Value

> *Retrieve the value occupying a specified position when values are ranked from highest to lowest.*

---

## 🎯 Problem

Return the **Nth highest distinct salary** from the `employees` table. 

Unlike a second-highest-value query, this pattern works for any requested position, such as the third, fifth, or tenth highest salary.

---

## 🤔 Mental Model 

Imagine sorting salaries from highest to lowest and assigning each distinct salary a position:

| Salary | Rank |     |
| -----: | ---: | --- |
| 150000 |    1 |     |
| 145000 |    2 |     |
| 130000 |    3 |     |

`DENSE_RANK()` assigns equal salaries the same rank without leaving gaps between the next rank values.

---
## 🧠 Why This Pattern Works

The window function ranks every salary in descending order. 

The outer query then filters the ranked results to the requested position. 

Because `DENSE_RANK()` gives duplicate values the same rank, this pattern ranks **distinct salary levels**, not individual employees.

---

## 💻 SQL Solution

```sql
SELECT DISTINCT salary 
FROM 
( 
	SELECT 
		salary, 
		DENSE_RANK() OVER ( 
			ORDER BY salary DESC 
		) AS salary_rank 
	FROM employees 
) AS ranked_salaries
WHERE salary_rank = 3;
```

Replace `3` with the desired rank.

---

## 🔄 Step-by-Step Breakdown

### Step 1: Order the salaries logically

```sql 
ORDER BY salary DESC 
``` 
The highest salary appears first. 
### Step 2: Assign ranks 

```sql 
DENSE_RANK() OVER ( 
	ORDER BY salary DESC 
) 
``` 
Equal salaries receive the same rank.
### Step 3: Filter for the requested rank 
```sql 
WHERE salary_rank = 3 
``` 
Only the third-highest distinct salary is returned. 

--- 
## 🧪 Example Data

| employee_name | salary |
|---|---:|
| Alice | 150000 |
| Bob | 150000 |
| Carol | 145000 |
| David | 130000 |

The rankings would be:

| salary | salary_rank |
|---:|---:|
| 150000 | 1 | 
| 150000 | 1 |
| 145000 | 2 | 
| 130000 | 3 |

Requesting rank `2` returns `145000`.

---

## 🚀 Common Use Cases

- Finding the third-highest salary 
- Ranking sales totals 
- Identifying top-performing products 
- Competition standings 
- Selecting the Nth largest measurement

---

## ⚖️ Alternatives

### `ROW_NUMBER()` 

```sql 
ROW_NUMBER() OVER ( 
	ORDER BY salary DESC 
) 
``` 

This ranks individual rows. Duplicate salaries receive different positions, so it answers a different question. 
### `RANK()` 

```sql 
RANK() OVER ( 
	ORDER BY salary DESC 
)
``` 

 Equal values share a rank, but later ranks may contain gaps. 
 
 For example: `1, 1, 3`. 
 
### `LIMIT` and `OFFSET` 
 
```sql 
 SELECT DISTINCT salary 
 FROM employees 
 ORDER BY salary DESC
  LIMIT 1 OFFSET 2; 
``` 

This retrieves the third-highest distinct salary in PostgreSQL and MySQL.

---

## ⚠️ Performance Considerations

- Ranking requires the database to process and order the qualifying rows. 
- An index on `salary` may help some execution plans. 
- Filter unnecessary rows before applying the window function. 
- Examine the execution plan when working with large tables.

---

## 🚨 Common Mistakes

- Using `ROW_NUMBER()` when duplicate salary values should share a rank. 
- Forgetting `DESC`, which would return the Nth lowest value. 
- Confusing the Nth employee with the Nth distinct salary. 
- Omitting `DISTINCT` in the outer query when only one salary value is wanted. 
- Requesting a rank that does not exist.

---

## 💡 Wisdom from Mímir

Before choosing a ranking function, define what “Nth highest” means. 
- Use `DENSE_RANK()` for the Nth **distinct value** without gaps. 
- Use `RANK()` when competition-style gaps are meaningful. 
- Use `ROW_NUMBER()` when every individual row needs a unique position. 

The hardest part is usually not writing the function. It is correctly defining how duplicates should be treated.

---

## 🔗 Related Notes

- [[Finding the Second Highest Value]] — the N=2 special case, contrasted with a simpler nested-MAX() approach
- [[Ranking the Top N Rows Within Each Group]] — the same DENSE_RANK windowing technique, partitioned per group instead of over the whole table
- [[Retrieving the Top N Highest Values]] — ORDER BY + LIMIT for "top N rows" instead of "the Nth distinct value"