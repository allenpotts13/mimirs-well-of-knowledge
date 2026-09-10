---
type: query-pattern
status: active
created: 2026-09-02
updated: 2026-09-02
technology: SQL
difficulty: Intermediate
tags:
  - subquery
  - having
  - group-by
  - threshold-filtering
aliases:
  - Departments With Fewer Than N Employees
  - Small Group Filtering
---

# 🧩 Finding Groups Below a Size Threshold

> *First identify which groups satisfy a size condition with `GROUP BY ... HAVING`, then pull back every individual row that belongs to those groups.*

---

## 🎯 Problem

Get employees who belong to departments with fewer than 3 employees.

More generally, this pattern answers:
> How do I return all the individual rows that belong to groups meeting some size condition?

---

## 🤔 Mental Model

Picture a bouncer at the door of a club, checking headcounts by group. First, the bouncer tallies how many people are in each group waiting outside and decides which groups meet the rule — "under 3 people, you're in." Only after that decision is made does anyone actually walk through the door: every member of a qualifying group is let in, one person at a time.

---

## 🧠 Why This Pattern Works

`HAVING` filters *groups*, not individual rows — it operates on the result of `GROUP BY`, after aggregation has already collapsed many rows into one summary row per group. That means a query using `GROUP BY ... HAVING` can tell you *which* department IDs have fewer than 3 employees, but it cannot, in that same grouped query, also hand back full employee detail rows — the individual rows no longer exist once they've been aggregated away.

The fix is to split the work into two steps. An inner subquery does the grouping and decides which `department_id` values qualify. The outer query then uses `WHERE department_id IN (...)` to go back to the ungrouped, row-level table and pull every employee belonging to one of those qualifying departments.

---

## 💻 SQL Solution

```sql
SELECT * FROM employees
WHERE department_id IN (
  SELECT department_id FROM employees
  GROUP BY department_id
  HAVING COUNT(*) < 3
);
```

The inner query never appears in the final output — it only supplies the list of department IDs the outer `WHERE ... IN` filters against.

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Group rows by department in the subquery

```sql
SELECT department_id FROM employees
GROUP BY department_id
```

MySQL collapses the table into one row per distinct `department_id`.

### Step 2 — Filter to qualifying groups with HAVING

```sql
HAVING COUNT(*) < 3
```

Only department groups whose row count is under 3 survive; the subquery now returns a short list of qualifying department IDs.

### Step 3 — Expand back to individual rows

```sql
WHERE department_id IN (...)
```

The outer query scans the full, ungrouped `employees` table again, keeping any row whose `department_id` appears in the qualifying list.

### Step 4 — Return full row detail

```sql
SELECT *
```

Every column for every employee in a small department is returned — not just the department ID.

---

## 🧪 Example Data

| employee_id | name    | department_id |
| ----------- | ------- | -------------- |
| 1           | Alice   | 101            |
| 2           | Bob     | 101            |
| 3           | Carol   | 102            |
| 4           | Dave    | 102            |
| 5           | Erin    | 102            |
| 6           | Frank   | 102            |
| 7           | Grace   | 103            |

Department headcounts: 101 has 2, 102 has 4, 103 has 1.

Query:
```sql
SELECT * FROM employees
WHERE department_id IN (
  SELECT department_id FROM employees
  GROUP BY department_id
  HAVING COUNT(*) < 3
);
```

Result:

| employee_id | name  | department_id |
| ----------- | ----- | -------------- |
| 1           | Alice | 101            |
| 2           | Bob   | 101            |
| 7           | Grace | 103            |

Department 102 has 4 employees and doesn't qualify, so Carol, Dave, Erin, and Frank are excluded even though the filter is applied at the department level, not the individual level.

---

## 🚀 Common Use Cases

- Identifying understaffed departments or teams for headcount planning
- Flagging small customer segments that might be worth merging for reporting
- Finding rarely-used product categories or tags for data cleanup
- Identifying managers with very few or very many direct reports
- Surfacing sparse groups (a course with few enrolled students, a cohort with few members) for review
- Data quality checks that look for groups falling outside an expected size range

---

## ⚖️ Alternatives

### Managers with more than 5 subordinates

```sql
SELECT * FROM employees
WHERE employee_id IN (
  SELECT manager_id FROM employees
  GROUP BY manager_id
  HAVING COUNT(*) > 5
);
```

Same two-step shape — qualify the group, then expand to its members — just flipped to a `>` threshold and grouped by `manager_id` instead of `department_id`. This is the general reusable idea behind the pattern: the direction of the threshold and the grouping column can change freely without changing the overall structure of the query.

### JOIN against a derived table instead of IN

```sql
SELECT e.*
FROM employees e
JOIN (
  SELECT department_id
  FROM employees
  GROUP BY department_id
  HAVING COUNT(*) < 3
) small_depts ON e.department_id = small_depts.department_id;
```

Functionally equivalent to the `IN` subquery version. Some query planners optimize a `JOIN` against a derived table differently than an `IN` subquery, and this form makes it easy to pull in additional grouped columns (like the group's row count) alongside the row-level detail if needed.

---

## ⚠️ Performance Considerations

- The inner subquery scans and groups the table once to build the qualifying list; MySQL often internally rewrites simple `IN` subqueries like this into a semi-join, which can be efficient even on large tables.
- An index on `department_id` helps both the grouping step and the outer lookup step.
- On very large tables where this qualifying-group check runs frequently, consider materializing the small-group list into a temporary table once and reusing it, rather than recomputing the subquery on every call.
- `HAVING COUNT(*) < 3` must scan every row in a group to count it — there's no way to answer "how many rows are in this group" without visiting the group's rows at least once.

---

## 🚨 Common Mistakes

- Trying to filter on `COUNT(*)` with `WHERE` instead of `HAVING` — aggregate functions cannot be referenced in `WHERE`, which filters rows before grouping happens.
- Forgetting the two-step shape entirely and trying to return individual row detail directly from a `GROUP BY ... HAVING` query, which only has grouped/aggregated columns available.
- Getting `<` and `<=` (or `>` and `>=`) confused, changing whether a group exactly at the threshold is included.
- Forgetting that rows with a `NULL` grouping value (a `NULL` `department_id` or `manager_id`) are grouped into their own bucket, which may or may not be intended.
- Assuming the subquery itself returns the employee rows — it only returns the qualifying group key; the outer query is what does the actual row-level selection.

---

## 💡 Wisdom from Mímir

A `HAVING` clause never hands you the rows you actually want — it only tells you which groups earned the right to be looked at more closely.

> First ask "which groups qualify?" Only then ask "who's in them?" Trying to answer both questions in the same breath is where this pattern goes wrong.

---

## 🔗 Related Notes

- [[Finding the Group With the Highest Count]]
- [[Counting Employees in Each Department]]
- [[Finding Groups With No Recent Activity]]
- [[Find the Total Number of Departments]]
