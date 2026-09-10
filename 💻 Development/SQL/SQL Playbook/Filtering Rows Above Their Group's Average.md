---
type: query-pattern
status: published
created: 2026-09-02
updated: 2026-09-02
technology: SQL
difficulty: Intermediate
tags:
  - correlated-subqueries
  - aggregate-functions
  - window-functions
  - grouping
aliases:
  - Employees Above Their Department Average
  - Correlated Subquery Pattern
publish: true
---

# <span class="rune">ᚲ</span> Filtering Rows Above Their Group's Average

> *Use a correlated subquery to compare each row against the average of its own group, rather than the average of the entire table.*

---

## 🎯 Problem

Find all employees who earn more than the average salary of their own department.

More generally, this pattern answers:
> Which rows exceed the typical value *within their own group*, rather than exceeding the typical value across everyone?

---

## 🤔 Mental Model

Imagine every department holding its own private staff meeting to calculate its own average salary — Sales calculates the Sales average, Engineering calculates the Engineering average, and so on. An employee is only compared against the number their own department produced, never against a company-wide figure.

A correlated subquery is exactly this: it recalculates the average freshly for whichever department the current employee happens to belong to.

---

## 🧠 Why This Pattern Works

The subquery here is **correlated** — notice that it references `e.department_id`, a column from the *outer* query. This means the subquery cannot be evaluated once and reused; conceptually, it must be re-evaluated for every row the outer query considers, each time using that row's own `department_id` to compute a department-specific average.

This is the key difference from a plain "values above the average" query, where the subquery is independent of the outer query and produces one fixed number for the whole table (see [[Finding Values Above the Average]]). Here, the average is a moving target — it depends on which row is currently being evaluated. That row-by-row dependency is what makes the subquery "correlated," and it's what lets the same query express a per-group comparison without an explicit `GROUP BY` in the outer query.

---

## 💻 SQL Solution

```sql
SELECT * FROM employees e
WHERE salary > (
  SELECT AVG(salary) FROM employees
  WHERE department_id = e.department_id
);
```

Aliasing the outer table as `e` makes the correlation (`e.department_id`) unambiguous and easy to spot at a glance.

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Alias the outer table

```sql
FROM employees e
```

Gives the outer query's rows a short name so the inner query can refer back to them.

### Step 2 — Correlate the inner query to the current row

```sql
WHERE department_id = e.department_id
```

For whichever employee row the outer query is currently checking, the inner query restricts itself to that same employee's department.

### Step 3 — Compute the group-specific average

```sql
SELECT AVG(salary) FROM employees WHERE department_id = e.department_id
```

Returns a single number: the average salary of just that one department.

### Step 4 — Compare the row against its own group's average

```sql
WHERE salary > (...)
```

The outer query keeps only employees whose individual salary exceeds their own department's average.

---

## 🧪 Example Data

| employee | department_id | salary |
| -------- | -------------: | -----: |
| Alice    |             10 |  70000 |
| Bob      |             10 |  50000 |
| Carol    |             20 |  90000 |
| Dave     |             20 |  60000 |

Department 10 average: (70000 + 50000) / 2 = 60000
Department 20 average: (90000 + 60000) / 2 = 75000

Query:
```sql
SELECT * FROM employees e
WHERE salary > (
  SELECT AVG(salary) FROM employees
  WHERE department_id = e.department_id
);
```

Result: Alice (70000 > 60000) and Carol (90000 > 75000).

---

## 🚀 Common Use Cases

- Employees earning above their department's average salary
- Products priced above the average price in their category
- Students scoring above their class's average grade
- Orders larger than a customer's own average order size
- Sales reps outperforming their region's average sales
- Reviews rated above a product's own average rating

---

## ⚖️ Alternatives

### Window function (recommended for larger tables)

```sql
SELECT * FROM (
  SELECT *, AVG(salary) OVER (PARTITION BY department_id) AS dept_avg
  FROM employees
) AS ranked
WHERE salary > dept_avg;
```

`AVG(salary) OVER (PARTITION BY department_id)` computes every department's average once, in a single pass, and attaches it to every row — no per-row re-execution required. This is generally the more efficient modern equivalent of the correlated subquery.

### Join against a pre-aggregated summary

```sql
SELECT e.*
FROM employees e
JOIN (
  SELECT department_id, AVG(salary) AS dept_avg
  FROM employees
  GROUP BY department_id
) d ON d.department_id = e.department_id
WHERE e.salary > d.dept_avg;
```

Computes each department's average exactly once as its own derived table, then joins it back — often faster than a correlated subquery because the aggregate work isn't repeated per row.

---

## ⚠️ Performance Considerations

- Correlated subqueries can be slow on large tables because, conceptually, the inner query re-executes once per outer row — for a million-row `employees` table, that's potentially a million small aggregate queries.
- MySQL's optimizer can sometimes rewrite a correlated subquery into a join internally, but this isn't guaranteed — always check the execution plan (`EXPLAIN`) before assuming it's efficient.
- An index on `department_id` (and ideally a covering index including `salary`) helps the inner query's per-row lookup considerably.
- For anything beyond a small table, prefer the window function or pre-aggregated join alternatives above — they compute each group's average exactly once.

---

## 🚨 Common Mistakes

- Forgetting the correlation entirely — writing `WHERE department_id = department_id` (no table alias) leaves the reference ambiguous or accidentally self-referential, and some databases will silently misinterpret it.
- Assuming this produces one row per department — it doesn't; it returns every *individual employee row* that beats their department's average, so multiple employees per department can appear.
- Confusing this with the plain "above the average" pattern and computing one company-wide average instead of a per-department one.
- Using `>=` when the intent was strictly "above average," which incorrectly includes employees earning exactly the average.
- Not considering the window function alternative on large tables, and paying an unnecessary performance cost for a correlated subquery that could be avoided.

---

## 💡 Wisdom from Mímir

"Above average" is meaningless without asking: **average of what population?**

> Is this employee unusual compared to the whole company, or merely unusual compared to the five people sitting next to them?

A correlated subquery forces you to answer that question explicitly, row by row — which is exactly why it's slower, and exactly why it's sometimes the only honest way to ask it.

---

## 🔗 Related Notes

- [[Finding Values Above the Average]]
- [[Finding the Highest Salary in Each Department]]
- [[Calculating a Total with SUM()]]
- [[Calculating Multiple Aggregates Per Group]]
- [[Finding Employees Who Earn More Than Their Manager]]
