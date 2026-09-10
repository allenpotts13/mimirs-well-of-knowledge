---
type: query-pattern
status: published
created: 2026-08-12
updated: 2026-08-12
technology: SQL
difficulty: Intermediate
tags:
  - group-by
  - count
  - order-by
  - limit
  - aggregation
aliases:
  - Department With the Most Employees
  - Highest Group Count
publish: true
---

# <span class="rune">ᚲ</span> Finding the Group With the Highest Count

> *Group rows by a category, count the members of each group, sort the counts from highest to lowest, and return the largest result.*

---

## 🎯 Problem

Find the department containing the greatest number of employees.

---

## 🤔 Mental Model

Imagine sorting employees into boxes by department. 

Then count how many employees are in each box: 

| Department | Employees |
|---|---:|
| IT | 27 | 
| Finance | 18 | 
| HR | 12 | 

Sort the boxes by count. 

The first box is the department with the most employees.

---

## 🧠 Why This Pattern Works

This query combines three operations: 
1. `GROUP BY` creates one group per department. 
2. `COUNT(*)` calculates the number of employees in each group. 
3. `ORDER BY ... DESC` places the largest count first. 
4. `LIMIT 1` returns only that highest group.

---

## 💻 SQL Solution

### PostgreSQL / MySQL
```sql
SELECT 
	department_id, 
	COUNT(*) AS employee_count 
FROM employees 
GROUP BY department_id 
ORDER BY COUNT(*) DESC 
LIMIT 1;
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Group employees 
```sql 
GROUP BY department_id 
``` 
### Step 2 — Count each group 
```sql 
COUNT(*) 
``` 

Example:

| department_id | employee_count |
|---:|---:|
| 10 | 12 |
| 20 | 27 |
| 30 | 18 | 

### Step 3 — Sort largest first 
```sql 
ORDER BY COUNT(*) DESC 
``` 

### Step 4 — Keep only the first result 
```sql 
LIMIT 1 
``` 

Result:

| department_id | employee_count |
|---:|---:|
| 20 | 27 |

--- 
## 🧪 SQL Server Variation

```sql 
SELECT TOP 1 
	department_id, 
	COUNT(*) AS employee_count 
FROM employees 
GROUP BY department_id 
ORDER BY COUNT(*) DESC; 
```

---

## 🚀 Common Use Cases

- Department with the most employees 
- Product with the most sales 
- Customer with the most orders 
- State with the most incidents 
- Category with the most records 
- Most frequently occurring value

---

## ⚖️ Alternatives

Return All Ties

Suppose two departments each contain 27 employees. 

A simple `LIMIT 1` returns only one of them. 

If all tied departments should be returned:
```sql 
WITH DepartmentCounts AS 
( 
	SELECT 
		department_id, 
		COUNT(*) AS employee_count 
	FROM employees 
	GROUP BY department_id 
), 
RankedDepartments AS 
( 
	SELECT 
		department_id, 
		employee_count, 
		DENSE_RANK() OVER ( 
			ORDER BY employee_count DESC 
		) AS department_rank 
	FROM DepartmentCounts 
) 
SELECT 
	department_id, 
	employee_count 
FROM RankedDepartments 
WHERE department_rank = 1; 
``` 

This returns every department tied for the largest employee count.

---

## ⚠️ Performance Considerations

- Grouping requires processing all qualifying rows. 
- Indexes on grouping columns may help. 
- Apply useful filters before aggregation when possible. 
- Large grouped datasets may require significant memory or sorting. 
- Avoid calculating more columns than the result requires.

---

## 🚨 Common Mistakes

- Forgetting `GROUP BY`. 
- Sorting ascending instead of descending. 
- Forgetting `LIMIT 1`. 
- Assuming `LIMIT 1` handles ties meaningfully. 
- Selecting nonaggregated columns that are not included in `GROUP BY`. 
- Counting the wrong entity.

---

## 💡 Wisdom from Mímir

Whenever a requirement says: 
> "Which group has the most...?" 

think: 
```text 
GROUP 
→ COUNT 
→ SORT DESC 
→ TOP 1 
``` 

But immediately ask one more question: 
> **What happens if there is a tie?** 

`LIMIT 1` answers "give me one winner." 

A ranking function answers "show me every winner." 

That business distinction matters.

---

## 🔗 Related Notes

- [[Counting Employees in Each Department]] — the per-group count this note then ranks and takes the top of
- [[Finding Groups Below a Size Threshold]] — the same GROUP BY + COUNT foundation, filtering by a size cutoff instead of finding the single largest
- [[Calculating Each Group's Percentage of the Total]] — another way to compare group sizes, as a share of the whole instead of a ranking