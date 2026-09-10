---
type: query-pattern
status: published
created: 2026-08-02
updated: 2026-08-02
technology: SQL
difficulty: Beginner
tags:
  - averages
  - aggregate-functions
  - subqueries
  - filtering
aliases:
  - Employees Earning Above Average
  - Comparing Values Against an Average
publish: true
permalink: sql/finding-values-above-the-average
---

# <span class="rune">ᚲ</span> Finding Values Above the Average

> *Return rows whose value is greater than the average calculated across a dataset.*

---

## 🎯 Problem

Return every employee whose salary is greater than the average salary of all employees.

---

## 🤔 Mental Model 

First, calculate the class average. 

Then compare each student's score against that single average. 

The SQL query follows the same sequence: 
1. Calculate one aggregate value. 
2. Compare every row against it. 
3. Keep only the rows above it. 

---
## 🧠 Why This Pattern Works

The subquery calculates one scalar value:

```sql 
SELECT AVG(salary) 
FROM employees 
``` 

The outer query evaluates each employee's salary against that result. 

Only employees whose salaries exceed the average are returned.

---

## 💻 SQL Solution

```sql
SELECT 
	employee_id, 
	employee_name, 
	salary 
FROM employees 
WHERE salary > ( 
	SELECT AVG(salary) 
	FROM employees 
);
```

---

## 🔄 Step-by-Step Breakdown

### Step 1: Calculate the average

```sql 
SELECT AVG(salary) 
FROM employees; 
``` 

Suppose this returns: 
```text 
75000 
``` 

### Step 2: Evaluate every employee 

The outer query effectively becomes: 
```sql 
WHERE salary > 75000 
``` 

### Step 3: Return qualifying rows

Only employees earning more than `75000` remain. 

## 🧪 Example Data 
| employee_name | salary |
|---|---:|
| Alice | 60000 | 
| Bob | 75000 |
| Carol | 90000 |
| David | 95000 |
Average salary: 
```text 
80000 
```

Returned employees:

| employee_name | salary |
|---|---:| 
| Carol | 90000 | 
| David | 95000 |

---

## 🚀 Common Use Cases

- Employees earning above average 
- Products selling above average 
- Students scoring above average 
- Orders exceeding average value 
- Locations performing above company averages

---

## ⚖️ Alternatives

### Compare against a departmental average 

A correlated subquery can compare each employee against the average for that employee’s department: 

```sql 
SELECT 
	e.employee_id, 
	e.employee_name, 
	e.department_id, 
	e.salary 
FROM employees e 
WHERE e.salary > ( 
	SELECT AVG(e2.salary) 
	FROM employees e2 
	WHERE e2.department_id = e.department_id 
);
```

### Window-function approach 

```sql 
SELECT 
	employee_id, 
	employee_name, 
	salary, 
	average_salary 
FROM
( 
	SELECT 
		employee_id, 
		employee_name, 
		salary, 
		AVG(salary) OVER () AS average_salary 
	FROM employees 
) AS salary_analysis 
WHERE salary > average_salary; 
```

The window-function version is useful when the average should also appear in the results.

---

## ⚠️ Performance Considerations

- A non-correlated scalar subquery may be calculated once by the optimizer. 
- A correlated subquery may execute more work because it depends on each outer row. 
- Window functions can be useful when both row-level and aggregate values are needed. 
- Filter irrelevant records before calculating the average when appropriate.

---

## 🚨 Common Mistakes

- Using `>=` when the requirement says strictly greater than average. 
- Forgetting that `AVG()` ignores `NULL` values. 
- Comparing every employee against the company average when the requirement calls for a departmental average. 
- Using `SELECT *` when only a few columns are needed. 
- Calculating the average from a differently filtered population than the outer query.

---

## 💡 Wisdom from Mímir

Always clarify the population used to calculate the average.

“Above average salary” could mean: 
- Above the entire company average 
- Above the employee’s department average 
- Above the average for a specific year 
- Above the average for active employees only 

The SQL may be simple, but an incorrectly defined comparison group produces a misleading result.

---

## 🔗 Related Notes

- [[Filtering Rows Above Their Group's Average]] — a dedicated note for the departmental (correlated subquery) variant sketched in this note's Alternatives section
- [[Calculating a Total with SUM()]] — the same "aggregate first, filter second" family, using SUM instead of AVG
- [[Finding the Highest Salary in Each Department]] — another "compare a row against a value computed from its own group" pattern