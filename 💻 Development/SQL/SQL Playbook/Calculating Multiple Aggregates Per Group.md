---
type: query-pattern
status: active
created: 2026-09-02
updated: 2026-09-02
technology: SQL
difficulty: Beginner
tags:
  - aggregate-functions
  - group-by
  - multiple-aggregates
  - reporting
aliases:
  - Multiple Aggregates in One Query
  - Group-Wise Min and Max
---

# 🧩 Calculating Multiple Aggregates Per Group

> *Compute several aggregate functions together in the same grouped query, instead of running a separate query per metric.*

---

## 🎯 Problem

Find the maximum salary and minimum salary in each department.

More generally, this pattern answers:
> How do I compute several summary statistics for each group at the same time?

---

## 🤔 Mental Model

Imagine sorting every employee's paperwork into labeled bins — one bin per department. Now imagine you don't walk past each bin once per statistic you want. Instead, you carry several calculators with you on a single walk-through: a max-o-meter, a min-o-meter, a sum-o-meter. You still only pass each pile of paper once — you're just reading more numbers off it while you're there.

---

## 🧠 Why This Pattern Works

`GROUP BY` partitions the rows of a table into buckets — one per distinct value of the grouping column. Every aggregate function listed in the `SELECT` clause is then evaluated independently against each bucket.

Because all of those aggregate functions run during the same grouped pass over the data, adding a second, third, or fourth aggregate doesn't require an additional scan of the table — the database is already visiting every row in the group to compute the first one.

---

## 💻 SQL Solution

```sql
SELECT department_id, MAX(salary), MIN(salary)
FROM employees
GROUP BY department_id;
```

Aliasing the aggregate columns (`MAX(salary) AS highest_salary`) makes the result set easier to read and safer to consume programmatically.

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Partition rows into groups

```sql
GROUP BY department_id
```

MySQL divides the `employees` table into one bucket per distinct `department_id`.

### Step 2 — Apply the first aggregate per group

```sql
MAX(salary)
```

Within each bucket, MySQL scans the `salary` values and keeps the highest one.

### Step 3 — Apply the second aggregate in the same pass

```sql
MIN(salary)
```

The same scan of the same bucket also tracks the lowest `salary` value — no separate pass over the data is needed.

### Step 4 — Return one row per group

Each department contributes exactly one output row, carrying its own `department_id`, max, and min.

---

## 🧪 Example Data

| employee | department_id | salary |
| -------- | -------------- | -----: |
| Alice    | 101            |  50000 |
| Bob      | 101            |  70000 |
| Carol    | 102            |  60000 |
| Dave     | 102            |  90000 |

Query:
```sql
SELECT department_id, MAX(salary) AS highest, MIN(salary) AS lowest
FROM employees
GROUP BY department_id;
```

Result:

| department_id | highest | lowest |
| -------------- | ------: | -----: |
| 101             |   70000 |  50000 |
| 102             |   90000 |  60000 |

---

## 🚀 Common Use Cases

- Reporting the salary band (min and max) for each department or job role
- Showing order value range (min/max/avg) per customer segment
- Building dashboard summary cards that need count, sum, and average together
- Comparing spread within categories, such as price min/max per product line
- Monthly reports combining total, average, and count for the same period
- Auditing data quality by comparing min and max against expected bounds per group

---

## ⚖️ Alternatives

### Department-wise total and average salary

```sql
SELECT department_id, SUM(salary) AS total_salary, AVG(salary) AS avg_salary
FROM employees
GROUP BY department_id;
```

Same pattern, different aggregate functions — a payroll total paired with an average, both computed per department in one pass.

### Highest, lowest, and average salary for each job role

```sql
SELECT job_role,
  MAX(salary) AS highest_salary,
  MIN(salary) AS lowest_salary,
  AVG(salary) AS avg_salary
FROM employees
GROUP BY job_role;
```

Three aggregates instead of two, grouped by a different column entirely — the shape of the query doesn't change as you add more statistics or swap the grouping column.

---

## ⚠️ Performance Considerations

- One `GROUP BY` pass computing several aggregates is cheaper than running one query per metric, since each separate query would re-scan and re-group the table from scratch.
- An index on the grouping column (`department_id`) helps MySQL form groups efficiently, especially with `GROUP BY`-friendly index ordering.
- A covering index that includes both the grouping column and the aggregated column (e.g., `(department_id, salary)`) can let MySQL satisfy the whole query from the index without touching the table.
- Adding more aggregate functions to an existing grouped query costs very little extra — the expensive part is the grouping itself, not the number of functions applied to each group.

---

## 🚨 Common Mistakes

- Running a separate query for each statistic instead of combining them, multiplying the number of scans needed for no benefit.
- Selecting a non-aggregated, non-grouped column (like an employee's name) alongside grouped aggregates, which is either an error or produces an arbitrary value depending on SQL mode.
- Forgetting aliases, leaving result columns labeled generically (`MAX(salary)`) and harder to consume downstream.
- Assuming every aggregate function handles `NULL` the same way — `MAX()`, `MIN()`, `SUM()`, and `AVG()` all ignore `NULL` values, but `COUNT(*)` does not, which can produce inconsistent-looking results when mixed together.
- Losing track of which alias belongs to which aggregate once several are combined in a wide report query.

---

## 💡 Wisdom from Mímir

The real cost of a grouped query is the grouping pass itself — not the number of aggregate functions riding along with it.

> "If you're already walking past every row in the group, what else can you learn while you're there?"

---

## 🔗 Related Notes

- [[Calculating a Total with SUM()]]
- [[Finding the Highest Salary in Each Department]]
- [[Counting Employees in Each Department]]
- [[Calculating Each Group's Percentage of the Total]]
- [[Aggregating Values Within a Date Range]]
