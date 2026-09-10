---
type: query-pattern
status: active
created: 2026-08-02
updated: 2026-08-02
technology: SQL
difficulty: Beginner
tags:
  - aggregate-functions
  - subqueries
  - max
  - ranking
aliases:
  - Second Highest Salary
  - Second Largest Value
---

# 🧩 Finding the Second Highest Value

>*Retrieve the second highest distinct value from a dataset without using sorting.*

---

## 🎯 Problem

Return the **second highest distinct salary** from the `employees` table. 

This pattern is commonly used in interviews, reporting, and ranking scenarios.

---

## 🧠 Why This Pattern Works

Imagine lining everyone up by salary. 
Remove the person earning the most. 
Now ask: 

> "Who is the highest paid person that's left?" 

That's exactly what this query does.

---

## 💻 SQL Solution

```sql
SELECT MAX(salary) 
FROM employees 
WHERE salary < 
( 
	SELECT MAX(salary) 
	FROM employees 
);
```

---

## 🔄 Step-by-Step Breakdown

1.
```sql 
 SELECT MAX(salary) 
 FROM employees; 
``` 
Finds the highest salary. 

Suppose the answer is: ``` 150000 ```

2.
The WHERE clause removes that value. ```sql salary < 150000 ``` 

Now the table no longer contains the highest salary.

3.
Run MAX() again. 

Now the highest remaining value is returned. ``` 145000 ``` 

Which is the second highest salary.

---

## 🚀 Common Use Cases

- Finding the runner-up 
- Employee salary reports 
- Sales leaderboards 
- Competition rankings 
- Reporting dashboards

---

## ⚖️ Alternatives

### Window Functions 
```sql 
SELECT salary 
FROM 
( 
	SELECT salary, 
		DENSE_RANK() OVER (ORDER BY salary DESC) AS salary_rank 
	FROM employees 
) ranked 
WHERE salary_rank = 2; 
``` 
**Pros** 
- Easier to extend to Top 10 
- Better for rankings 
**Cons** 
- Slightly more advanced 
--- 
### ORDER BY with LIMIT / OFFSET 
Some databases support:
```sql 
SELECT DISTINCT salary 
FROM employees 
ORDER BY salary DESC 
LIMIT 1 OFFSET 1; 
``` 
Works well in PostgreSQL and MySQL.

---

## ⚠️ Performance Considerations

For small tables this query performs well. 

On larger datasets: 
- Index the salary column. 
- Window functions may be easier to maintain. 
- Compare execution plans when performance matters.

---

## 🚨 Common Mistakes

### Duplicate Salaries 

If two employees share the highest salary: ``` 150000, 150000, 145000 ``` 

This query still returns **145000** because MAX() returns the highest **distinct** value below the maximum. 

--- 
### Forgetting DISTINCT 

Some ORDER BY approaches accidentally return duplicate salaries. 

--- 
### Choosing the Wrong Pattern 

If you need the **Nth** highest value rather than the second highest, use **DENSE_RANK()** instead.

---

## 💡 Wisdom from Mímir

This pattern is excellent for interview questions because it demonstrates an understanding of aggregate functions and subqueries. 

In production code, however, I generally prefer **DENSE_RANK()** when rankings may expand beyond the second value. It scales better, is easier to modify, and clearly communicates the intent of the query. 

Whenever I see a request like: 

> "Show me the 3rd highest..." 

 or 
 
> "Show me the Top 10..." 

my first thought is **Window Functions**, not nested MAX() queries.

---

## 🔗 Related Notes

- [[Finding the Nth Highest Value]] — the general form of this same problem
- [[Ranking the Top N Rows Within Each Group]] — the same DENSE_RANK technique, applied per department instead of company-wide
- [[Retrieving the Top N Highest Values]] — the simpler ORDER BY + LIMIT version when ties don't need special handling