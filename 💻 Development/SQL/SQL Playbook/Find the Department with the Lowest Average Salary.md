---
type: query-pattern
status: published
created: 2026-08-13
updated: 2026-08-13
technology: SQL
difficulty: Intermediate
tags:
  - query-pattern
  - aggregation
  - group-by
  - avg
aliases:
  - Lowest Average Salary by Department
  - Department with Lowest Average Salary
publish: true
permalink: sql/find-the-department-with-the-lowest-average-salary
---

# <span class="rune">ᚲ</span> Find the Department with the Lowest Average Salary

> *A reusable SQL solution for finding the department with the lowest average salary.*

---

## 🎯 Problem

How can we determine which department has the lowest average salary?

---

## 🤔 Mental Model

We need to:

1. Group employees by department.
2. Calculate the average salary for each department.
3. Sort those averages from lowest to highest.
4. Return the lowest result.


---

## 🧠 Why This Pattern Works

`GROUP BY` creates a separate group for each department.

`AVG(salary)` calculates the average salary within each group.

Then `ORDER BY` sorts the department averages.

Finally, `LIMIT 1` returns the department with the lowest average.

---

## 💻 SQL Solution

```sql
SELECT
    department_id,
    AVG(salary) AS average_salary
FROM employees
GROUP BY department_id
ORDER BY AVG(salary) ASC
LIMIT 1;
```

---

## 🔄 Step-by-Step Breakdown

1. Group employees by department.

```sql
GROUP BY department_id
```

2. Calculate the average salary for each department.

```sql
AVG(salary)
```

3. Sort the averages from lowest to highest.

```sql
ORDER BY AVG(salary) ASC
```

4. Return the department with the lowest average.

```sql
LIMIT 1
```

The pattern:

Employees

    ↓

GROUP BY department

    ↓

AVG(salary)

    ↓

ORDER BY lowest

    ↓

LIMIT 1

---

## 🚀 Common Use Cases

- Salary analysis
- Department comparisons
- HR reporting
- Compensation analysis
- Business intelligence

---

## ⚖️ Alternatives

A window function can be useful when ties need to be preserved:

```sql
SELECT *
FROM (
    SELECT
        department_id,
        AVG(salary) AS average_salary,
        RANK() OVER (
            ORDER BY AVG(salary)
        ) AS salary_rank
    FROM employees
    GROUP BY department_id
) ranked
WHERE salary_rank = 1;
```

This can return multiple departments if they share the same lowest average salary.

---

## ⚠️ Performance Considerations

- `GROUP BY` requires aggregation across the employee records.
- An index on `department_id` may help.
- Large datasets can make aggregation expensive.
- Check the execution plan when working with large tables.

---

## 🚨 Common Mistakes

- Finding the lowest individual salary instead of the lowest average.
- Forgetting `GROUP BY`.
- Using `MIN(salary)` instead of `AVG(salary)`.
- Forgetting `LIMIT 1`.
- Ignoring ties between departments.

---

## 💡 Wisdom from Mímir

> _"Aggregate first, compare second."_

When a question asks which group has the highest, lowest, or average value, think:

GROUP BY

   ↓

Aggregate

   ↓

ORDER BY

   ↓

LIMIT / RANK


---

## 🔗 Related Notes

- [[Finding the Highest Salary in Each Department]] — the same GROUP BY + aggregate-comparison mental model, at the opposite end and at the employee level rather than the department level
- [[Calculating Multiple Aggregates Per Group]] — computes AVG alongside other aggregates per department instead of ranking departments by AVG alone
- [[Filtering Rows Above Their Group's Average]] — reuses this note's AVG-per-group idea, but to filter individual rows rather than rank departments