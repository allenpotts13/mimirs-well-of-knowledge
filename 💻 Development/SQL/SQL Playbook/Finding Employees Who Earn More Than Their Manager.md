---
type: query-pattern
status: published
created: 2026-09-02
updated: 2026-09-02
technology: SQL
difficulty: Intermediate
tags:
  - self-join
  - joins
  - salary-comparison
  - hierarchical-data
aliases:
  - Employees Who Out-Earn Their Boss
  - Overpaid Subordinates
publish: true
permalink: sql/finding-employees-who-earn-more-than-their-manager
---

# <span class="rune">ᚲ</span> Finding Employees Who Earn More Than Their Manager

> *Use a self-join to place each employee next to their manager, then compare their salaries directly.*

---

## 🎯 Problem

Find employees whose salary is higher than their manager's salary.

More generally, this pattern answers:
> How do I compare a row's value against a related row's value in the same table?

---

## 🤔 Mental Model

Line every employee up next to their manager, like measuring two people back to back against a wall. Whoever's mark is higher wins the comparison — except here you're comparing paychecks instead of height, and you need the two people standing next to each other before you can compare them at all.

---

## 🧠 Why This Pattern Works

An employee's salary and their manager's salary live in the same table, on two different rows, connected only by `manager_id` pointing back to another `employee_id`. Before any comparison can happen, those two rows have to be brought into the same row of output.

A self-join does exactly that: aliasing the `employees` table twice — once as the employee (`e`), once as the manager (`m`) — and joining them on `e.manager_id = m.employee_id` produces one combined row per employee containing both salaries side by side. From there, `e.salary > m.salary` is just an ordinary comparison.

---

## 💻 SQL Solution

```sql
SELECT e.employee_id, e.name
FROM employees e
JOIN employees m ON e.manager_id = m.employee_id
WHERE e.salary > m.salary;
```

`e` and `m` are both the same table under the hood — the aliases are what let the query treat them as two distinct roles.

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Alias the table for both roles

```sql
FROM employees e
JOIN employees m
```

MySQL prepares to treat the `employees` table as two logically separate sets: employees and managers.

### Step 2 — Link each employee to their manager's row

```sql
ON e.manager_id = m.employee_id
```

For each employee, MySQL finds the row where that manager's own `employee_id` matches, producing one row that holds both people's data together.

### Step 3 — Compare the two salaries

```sql
WHERE e.salary > m.salary
```

MySQL keeps only the rows where the employee's salary column is strictly greater than the manager's.

### Step 4 — Return the qualifying employees

```sql
SELECT e.employee_id, e.name
```

Only the employee's own identifying columns are returned, even though the manager's salary was needed to decide inclusion.

---

## 🧪 Example Data

| employee_id | name  | manager_id | salary |
| ----------- | ----- | ---------- | -----: |
| 1           | Alice | NULL       |  90000 |
| 2           | Bob   | 1          |  95000 |
| 3           | Carol | 1          |  80000 |

Query:
```sql
SELECT e.employee_id, e.name
FROM employees e
JOIN employees m ON e.manager_id = m.employee_id
WHERE e.salary > m.salary;
```

Result:

| employee_id | name |
| ----------- | ---- |
| 2           | Bob  |

Bob earns 95000 against his manager Alice's 90000, so he qualifies. Carol earns less than Alice and is excluded. Alice herself has no manager row to compare against, so she never appears on either side of this result.

---

## 🚀 Common Use Cases

- Flagging pay compression or pay inversion issues for HR and compensation review
- Auditing organizational data where reporting-line pay should generally increase upward
- Comparing a salesperson's revenue against their team lead's targets
- Surfacing anomalies in any hierarchy where a "subordinate" metric shouldn't outpace its "parent"
- Feeding compensation-review dashboards that need to explain why a pay gap exists
- Sanity-checking data migrations that load salary and reporting-line data separately

---

## ⚖️ Alternatives

### Also flagging employees with no manager

```sql
SELECT e.employee_id, e.name,
  CASE WHEN m.employee_id IS NULL THEN 'No manager' ELSE NULL END AS note
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.employee_id
WHERE m.employee_id IS NULL OR e.salary > m.salary;
```

Because an inner `JOIN` requires a matching manager row, employees at the top of the hierarchy (like a CEO with `manager_id IS NULL`) never appear in the base query's result at all. This `LEFT JOIN` version keeps them visible instead of silently dropping them.

### Showing the pay gap amount

```sql
SELECT e.employee_id, e.name,
  e.salary - m.salary AS pay_gap
FROM employees e
JOIN employees m ON e.manager_id = m.employee_id
WHERE e.salary > m.salary;
```

Rather than just a yes/no filter, this surfaces how large the gap is, which is often more useful for a compensation review than the raw list of names.

---

## ⚠️ Performance Considerations

- The self-join effectively scans the table twice; an index on `manager_id` (and the primary key `employee_id`) keeps the join efficient as the table grows.
- The `WHERE e.salary > m.salary` filter is a plain column comparison with no function wrapping either side, so it stays sargable and index-friendly.
- On large organizational tables, this query only compares each employee to their direct manager — it does not need to traverse the full hierarchy, so it scales linearly with the number of employees rather than the depth of the org chart.

---

## 🚨 Common Mistakes

- Using `JOIN` instead of `LEFT JOIN` when the goal is to also account for employees with no manager — the inner join silently excludes them from the result entirely rather than flagging them.
- Reversing the comparison (`m.salary > e.salary`) and accidentally finding managers who out-earn their reports instead of the reverse.
- Forgetting that this only compares one level of hierarchy — it does not detect a skip-level employee out-earning a manager two levels up.
- Treating `>=` and `>` as interchangeable, which changes whether employees who earn exactly the same as their manager are included.
- Assuming a `NULL` `manager_id` will simply "not match" harmlessly — it does exactly that, but it also means those employees vanish from the result set without any error or warning.

---

## 💡 Wisdom from Mímir

The org chart describes who reports to whom. It makes no promise about who earns what.

> This query doesn't explain the gap — it only points at where authority and compensation have quietly diverged. What you do with that gap is a decision no `WHERE` clause can make for you.

---

## 🔗 Related Notes

- [[Comparing Related Rows With a Self-Join]]
- [[Find All Employees Who Report to a Specific Manager]]
- [[Finding the Highest Salary in Each Department]]
- [[Finding Peers Who Share the Same Manager]]
