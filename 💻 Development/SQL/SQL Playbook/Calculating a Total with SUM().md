---
type: query-pattern
status: published
created: 2026-08-12
updated: 2026-08-12
technology: SQL
difficulty: Beginner
tags:
  - aggregate-functions
  - sum
  - totals
  - reporting
aliases:
  - Total Salary of Employees
  - SUM Function
publish: true
---

# <span class="rune">ᚲ</span> Calculating a Total with SUM()

> *Use `SUM()` to add the numeric values from multiple rows and return a single total.*

---

## 🎯 Problem

Calculate the combined salary of all employees in the `employees` table. 

More generally, this pattern answers: 
> What is the total value across a set of rows?

---

## 🤔 Mental Model

Imagine writing every employee salary on a piece of paper and adding them together. 

`SUM()` performs that addition for you.

---

## 🧠 Why This Pattern Works

`SUM()` is an aggregate function. 

Instead of returning one result per row, it processes the qualifying rows as a group and returns a single numeric result.

---

## 💻 SQL Solution

```sql
SELECT SUM(salary) AS total_salary 
FROM employees;
```

Using an alias such as `total_salary` makes the result easier to understand.

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Select the numeric column 

```sql 
salary 
``` 

### Step 2 — Apply the aggregate function

```sql 
SUM(salary) 
``` 

The database adds the salary values together. 

### Step 3 — Return one result 

Example: 
```text 
4250000 
``` 

--- 
## 🧪 Example Data 

| employee | salary |     |
| -------- | -----: | --- |
| Alice    |  60000 |     |
| Bob      |  75000 |     |
| Carol    |  90000 |     |

Query: 
```sql 
SELECT SUM(salary) 
FROM employees; 
``` 

Result: 
```text 
225000 
```

---

## 🚀 Common Use Cases

- Total payroll 
- Total sales 
- Total inventory value 
- Total transaction amount 
- Total hours worked 
- Total expenses

---

## ⚖️ Alternatives

### Grouped totals

```sql 
SELECT 
	department_id, 
	SUM(salary) AS total_salary 
FROM employees 
GROUP BY department_id; 
``` 

This produces one total per department rather than one total for the entire table. 

### Conditional totals 

```sql 
SELECT SUM(salary) 
FROM employees 
WHERE department_id = 10; 
``` 

This limits the rows included in the total.

---

## ⚠️ Performance Considerations

- Aggregating very large tables requires reading the qualifying rows. 
- Indexes may help if the query includes a selective `WHERE` clause. 
- Avoid calculating totals over more rows than necessary. 
- Pre-aggregated tables may be useful for very large reporting workloads.

---

## 🚨 Common Mistakes

- Using `SUM()` on nonnumeric data. 
- Forgetting a `WHERE` clause when only a subset should be totaled. 
- Confusing `SUM()` with `COUNT()`. 
- Grouping unnecessarily when only one total is needed. 
- Forgetting that `NULL` values are ignored by `SUM()`.

---

## 💡 Wisdom from Mímir

The important question is not simply: 
> "What should I sum?"

 It is: 
 > **Which rows belong in the total?** 

A technically correct `SUM()` can still produce the wrong business answer if the query includes the wrong population.

---

## 🔗 Related Notes

- [[Calculating Multiple Aggregates Per Group]] — extends the "Grouped totals" alternative shown here into MAX/MIN/AVG computed together per group
- [[Aggregating Values Within a Date Range]] — the same SUM + GROUP BY shape, scoped to a rolling date window
- [[Calculating Each Group's Percentage of the Total]] — turns a per-group total into a share of the overall total