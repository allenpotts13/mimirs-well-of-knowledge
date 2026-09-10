---
type: query-pattern
status: active
created: 2026-09-02
updated: 2026-09-02
technology: SQL
difficulty: Intermediate
tags:
  - self-join
  - joins
  - hierarchical-data
  - employees-table
aliases:
  - Self-Join Pattern
  - Comparing a Row to Its Related Row
---

# 🧩 Comparing Related Rows With a Self-Join

> *Join a table to itself, aliased as two different roles, to compare each row against another related row in the same table.*

---

## 🎯 Problem

Find employees who joined the company in the same month and year as their manager.

More generally, this pattern answers:
> How do I compare each row in a table to another row in that same table that it's related to?

---

## 🤔 Mental Model

Photocopy the table and lay the copy on top of the original. Label one stack "employee" and the other "manager." Then draw a line from each employee's row to the manager's row it points to. Once that line exists, you can compare anything you like across it — dates, salaries, titles — even though both rows come from the exact same physical table.

---

## 🧠 Why This Pattern Works

SQL has no built-in concept of "manager" or "employee" — it only sees a table with a column, `manager_id`, whose values happen to match `employee_id` values in the very same table. A self-join makes that hierarchical relationship explicit by aliasing the table twice, once for each logical role, and joining those two aliases on the relationship column.

Once joined, each output row physically contains both an employee's columns and their manager's columns side by side, so any comparison between the two — equal dates, higher salary, matching department — becomes an ordinary `WHERE` condition.

---

## 💻 SQL Solution

```sql
SELECT e.employee_id, e.name
FROM employees e
JOIN employees m ON e.manager_id = m.employee_id
WHERE MONTH(e.join_date) = MONTH(m.join_date)
  AND YEAR(e.join_date) = YEAR(m.join_date);
```

Aliasing the same table as `e` (employee) and `m` (manager) is what makes it possible to reference both roles' columns separately in the same query.

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Alias the table twice

```sql
FROM employees e
JOIN employees m
```

MySQL treats `e` and `m` as two independent, logically separate copies of the same underlying table.

### Step 2 — Link each employee to their manager's row

```sql
ON e.manager_id = m.employee_id
```

For every employee row, MySQL finds the row in the "manager" copy whose `employee_id` matches that employee's `manager_id`, producing one combined row per employee-manager pair.

### Step 3 — Compare the two related rows

```sql
WHERE MONTH(e.join_date) = MONTH(m.join_date)
  AND YEAR(e.join_date) = YEAR(m.join_date)
```

With both dates available in the same row, MySQL checks whether the employee's `join_date` shares the same month and year as their manager's.

### Step 4 — Return the matching employees

```sql
SELECT e.employee_id, e.name
```

Only the employee-side columns are selected, even though the manager's columns were used to filter.

---

## 🧪 Example Data

| employee_id | name  | manager_id | join_date  |
| ----------- | ----- | ---------- | ---------- |
| 1           | Alice | NULL       | 2020-01-10 |
| 2           | Bob   | 1          | 2020-01-22 |
| 3           | Carol | 1          | 2021-06-05 |

Query:
```sql
SELECT e.employee_id, e.name
FROM employees e
JOIN employees m ON e.manager_id = m.employee_id
WHERE MONTH(e.join_date) = MONTH(m.join_date)
  AND YEAR(e.join_date) = YEAR(m.join_date);
```

Result:

| employee_id | name |
| ----------- | ---- |
| 2           | Bob  |

Bob joined in January 2020, the same month and year as his manager Alice. Carol joined in a different month and year, so she's excluded — and Alice herself has no manager row to join against.

---

## 🚀 Common Use Cases

- Comparing an employee's attributes against their manager's (dates, salary, department)
- Comparing a product to a previous or related version stored in the same table
- Comparing a student to their assigned mentor or advisor
- Finding pairs of rows that share an attribute within a self-referencing hierarchy
- Auditing organizational data for anomalies between related rows
- Building peer or lineage comparisons in any table with a self-referencing foreign key

---

## ⚖️ Alternatives

### Comparing salary instead of join date

```sql
SELECT e.employee_id, e.name
FROM employees e
JOIN employees m ON e.manager_id = m.employee_id
WHERE e.salary > m.salary;
```

The exact same join shape, applied to a comparison condition instead of an equality match. See [[Finding Employees Who Earn More Than Their Manager]] for the full treatment of this variant.

### LEFT JOIN to also see employees without a manager

```sql
SELECT e.employee_id, e.name, m.join_date AS manager_join_date
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.employee_id;
```

An inner `JOIN` silently drops any employee whose `manager_id` is `NULL` (typically the top of the hierarchy), since there's no matching manager row to join against. A `LEFT JOIN` keeps those employees in the result, with `NULL` standing in for the missing manager columns.

---

## ⚠️ Performance Considerations

- A self-join effectively scans the same table twice; an index on both the primary key (`employee_id`) and the self-referencing foreign key (`manager_id`) is important for an efficient join.
- Wrapping the join columns in functions like `MONTH()` and `YEAR()` in the `WHERE` clause prevents MySQL from using a standard index on `join_date` for that filter (the condition becomes non-sargable).
- On small hierarchy tables the cost is negligible; on very large employee or organizational tables, consider a computed/generated column for month-year comparisons if this filter runs frequently.
- Self-joins on deep hierarchies only reach one level; comparing across multiple levels of management requires either repeated joins or a recursive CTE.

---

## 🚨 Common Mistakes

- Mixing up which alias represents "the row" and which represents "the related row," flipping the intended comparison direction.
- Using `JOIN` instead of `LEFT JOIN` when rows without a match (like an employee with no manager) should still appear in the result.
- Forgetting to alias columns, causing "ambiguous column" errors since both copies of the table share identical column names.
- Assuming a single self-join can walk multiple levels of a hierarchy — it only connects one row to one directly related row.
- Wrapping date or other columns in functions inside the join condition or filter, quietly disabling index usage on large tables.

---

## 💡 Wisdom from Mímir

A self-join isn't really about joining a table to itself — it's about giving one dataset two names, so a query can ask a question that needs both roles present at once.

> Before writing the `ON` clause, ask: which column is playing which part?

---

## 🔗 Related Notes

- [[Finding Employees Who Earn More Than Their Manager]]
- [[Find All Employees Who Report to a Specific Manager]]
- [[Finding Peers Who Share the Same Manager]]
- [[Filtering Records by Year]]
