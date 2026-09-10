---
type: query-pattern
status: published
created: 2026-08-13
updated: 2026-08-13
technology: SQL
difficulty: Intermediate
tags:
  - correlated-subquery
  - max
  - group-by
  - subqueries
  - salary
aliases:
  - Highest Salary Per Department
  - Maximum Salary by Department
publish: true
permalink: sql/finding-the-highest-salary-in-each-department
---

# <span class="rune">ᚲ</span> Finding the Highest Salary in Each Department

> *Use a correlated subquery to compare each employee's salary against the maximum salary for that employee's department.*

---

## 🎯 Problem

Find the employee or employees earning the highest salary in each department.

---

## 🤔 Mental Model

For every employee:

> "What is the highest salary in **this employee's department**?"

Then ask:

> "Does this employee's salary equal that value?"

If yes, return the employee.


---

## 🧠 Why This Pattern Works

The outer query examines employees.

The inner query calculates the maximum salary for the department belonging to the current employee.

The inner query is therefore **correlated** with the outer query.

---

## 💻 SQL Solution

```sql
SELECT
    department_id,
    employee_id,
    salary
FROM employees AS e
WHERE salary = (
    SELECT MAX(salary)
    FROM employees
    WHERE department_id = e.department_id
);
```

---

## 🔄 Step-by-Step Breakdown

### Outer Query

```sql
SELECT
    department_id,
    employee_id,
    salary
FROM employees AS e
```

This examines each employee.

### Inner Query

```sql
SELECT MAX(salary)
FROM employees
WHERE department_id = e.department_id
```

This asks:

> What is the maximum salary in this employee's department?

### Final Comparison

```sql
WHERE salary = (...)
```

Only employees whose salary equals their department's maximum are returned.

---

## 🧪 Important Edge Case — Ties

Suppose Department 10 has:

| Employee | Salary |
|---|---:|
| Alice | 100000 |
| Bob | 125000 |
| Carol | 125000 |

Both Bob and Carol are returned.

That's because:

```sql
salary = MAX(salary)
```

matches both employees.

This is often exactly what you want.

---

## 🚀 Common Use Cases

- Highest-paid employee per department
- Most expensive product per category
- Largest transaction per customer
- Most recent order per customer
- Highest-scoring employee per team

---

## ⚖️ Alternatives

### Windows Function

```sql
SELECT
    department_id,
    employee_id,
    salary
FROM (
    SELECT
        department_id,
        employee_id,
        salary,
        DENSE_RANK() OVER (
            PARTITION BY department_id
            ORDER BY salary DESC
        ) AS salary_rank
    FROM employees
) AS ranked
WHERE salary_rank = 1;
```

This is especially useful when you're already doing other ranking operations.

---

## ⚠️ Performance Considerations

- The correlated subquery may execute logically for each outer row, although optimizers can transform it.
- Window functions can be more efficient for some datasets and database engines.
- An index involving `department_id` and `salary` may help.
- Always check the execution plan for large tables rather than assuming one approach is faster.

---

## 🚨 Common Mistakes

- Forgetting to correlate the subquery with the outer query.
- Finding the highest salary across the entire company instead of each department.
- Using `LIMIT 1`, which would return only one employee overall.
- Not considering ties.

---

## 💡 Wisdom from Mímir

Whenever you see:

> **"Highest X in each Y"**

the phrase **"in each"** is the clue.

You're not looking for one global maximum.

You're looking for a maximum **within each group**.

Think:

```text
MAX
+
PER GROUP
```

---

## 🔗 Related Notes

- [[Ranking the Top N Rows Within Each Group]] — the DENSE_RANK window-function alternative shown in this note's Alternatives section, generalized to any N
- [[Calculating Multiple Aggregates Per Group]] — the same "per department" aggregation idea, computing MAX/MIN/AVG together instead of filtering rows against MAX
- [[Find the Department with the Lowest Average Salary]] — the same correlated-comparison mental model, applied at the department level instead of the employee level