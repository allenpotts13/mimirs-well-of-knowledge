---
type: query-pattern
status: published
created: 2026-09-02
updated: 2026-09-02
technology: SQL
difficulty: Advanced
tags:
  - relational-division
  - having
  - group-by
  - count-distinct
aliases:
  - Relational Division
  - Employees in Multiple Departments
  - Matching All Values in a Set
publish: true
---

# 🧩 Finding Records That Match Every Value in a Set

> *Group by the entity, then use `HAVING COUNT(DISTINCT ...) = N` to keep only groups that touch every one of the N required values.*

---

## 🎯 Problem

Find all employees who work in both department 101 and department 102 (assuming employees can have multiple department-assignment rows in this table).

More generally, this pattern answers:
> Which entities are associated with **every** member of a required set, not just *any* member of it?

---

## 🤔 Mental Model

Think of it as a punch card. Each required value — department 101, department 102 — is a hole that needs punching. An employee's rows punch holes for whichever departments they're assigned to. Only employees whose card has *every* required hole punched pass the check. An employee assigned to just department 101 has an incomplete card and is rejected, even though they technically match one of the values.

This is why the technique is sometimes called **relational division**: you're dividing the employee-department rows by the required set of departments, and only entities that divide evenly (touch all of them) remain.

---

## 🧠 Why This Pattern Works

`WHERE department_id IN (101, 102)` first narrows the rows down to only those touching a relevant department — this is an "any" filter, not yet an "all" filter. Grouping by `employee_id` then collapses each employee's remaining rows into one group per person.

The key step is `HAVING COUNT(DISTINCT department_id) = 2`. Because the `WHERE` clause already guarantees every remaining row's `department_id` is either 101 or 102, counting the *distinct* department values per employee tells you how many of the two required departments that employee actually touched. An employee in both shows a distinct count of 2; an employee in only one shows a distinct count of 1 and gets filtered out by `HAVING`.

---

## 💻 SQL Solution

```sql
SELECT employee_id
FROM employees
WHERE department_id IN (101, 102)
GROUP BY employee_id
HAVING COUNT(DISTINCT department_id) = 2;
```

Using `COUNT(DISTINCT department_id)` rather than plain `COUNT(department_id)` matters here — it protects against double-counting if an employee somehow has duplicate rows for the same department.

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Narrow to relevant rows only

```sql
WHERE department_id IN (101, 102)
```

Rows belonging to any department outside the required set are discarded immediately; this is an "any of these" filter, not the final answer.

### Step 2 — Group the remaining rows by entity

```sql
GROUP BY employee_id
```

All remaining department-assignment rows for a given employee collapse into a single group.

### Step 3 — Count how many distinct required values each entity touched

```sql
COUNT(DISTINCT department_id)
```

Within each employee's group, this counts how many *unique* departments (out of the 101/102 subset already filtered in) that employee has a row for.

### Step 4 — Keep only entities that touched all required values

```sql
HAVING COUNT(DISTINCT department_id) = 2
```

Only employees whose distinct count equals the full size of the required set — 2, matching the two values in the `IN` list — survive.

---

## 🧪 Example Data

| employee_id | department_id |
| ----------- | -------------- |
| 1           | 101            |
| 1           | 102            |
| 2           | 101            |
| 3           | 101            |
| 3           | 102            |
| 3           | 103            |
| 4           | 103            |

Query:
```sql
SELECT employee_id
FROM employees
WHERE department_id IN (101, 102)
GROUP BY employee_id
HAVING COUNT(DISTINCT department_id) = 2;
```

Walkthrough: after the `WHERE` filter, employee 4's only row (department 103) is dropped entirely. Grouping the rest gives employee 1 → {101, 102}, employee 2 → {101}, employee 3 → {101, 102} (its department 103 row was already excluded by `WHERE`). Result:

| employee_id |
| ----------- |
| 1           |
| 3           |

Employee 2 is excluded (only touches department 101); employee 3 is included even though they also belong to department 103, because the `WHERE` clause never let that row count against them.

---

## 🚀 Common Use Cases

- Finding students enrolled in *every* course in a required set
- Finding customers who purchased *all* products in a specific bundle
- Finding suppliers who stock *every* part needed for an assembly (classic relational-division / bill-of-materials problem)
- Finding users who hold *all* required certifications or permissions
- Finding projects staffed with *all* mandatory role types

---

## ⚖️ Alternatives

### Self-join per required value

```sql
SELECT DISTINCT e1.employee_id
FROM employees e1
JOIN employees e2
    ON e1.employee_id = e2.employee_id
WHERE e1.department_id = 101
  AND e2.department_id = 102;
```

This joins the table to itself once per required value, explicitly pairing up an employee's row in department 101 with their row in department 102. It works well for a small, fixed set of required values but becomes unwieldy fast — adding a third required department means adding a whole extra join, not just editing a number.

### NOT EXISTS against the required set (true relational division)

```sql
SELECT DISTINCT e.employee_id
FROM employees e
WHERE NOT EXISTS (
    SELECT d.department_id
    FROM (SELECT 101 AS department_id UNION SELECT 102) AS d
    WHERE NOT EXISTS (
        SELECT 1
        FROM employees e2
        WHERE e2.employee_id = e.employee_id
          AND e2.department_id = d.department_id
    )
);
```

This is the textbook "double NOT EXISTS" relational-division form: it finds employees for whom there is no required department that they're *missing*. It's more verbose but scales cleanly to large or dynamically-sized required sets without hardcoding a count anywhere.

### COUNT(DISTINCT ...) against a full table scan (no pre-filter)

```sql
SELECT employee_id
FROM employees
GROUP BY employee_id
HAVING SUM(department_id = 101) > 0
   AND SUM(department_id = 102) > 0;
```

Rather than pre-filtering with `WHERE department_id IN (...)`, this groups *all* of an employee's rows and uses conditional `SUM()` expressions to check each required value independently. It reads slightly more explicitly value-by-value, at the cost of grouping over every row rather than just the relevant subset.

---

## ⚠️ Performance Considerations

- An index on `department_id` (or a composite index on `(department_id, employee_id)`) helps the initial `WHERE department_id IN (...)` filter narrow the table quickly before grouping.
- `GROUP BY employee_id` with `HAVING` requires MySQL to aggregate every remaining row, so pre-filtering with `WHERE` first (rather than grouping the whole table) meaningfully reduces the aggregation workload.
- The self-join alternative grows one extra join per required value, which can produce a much larger intermediate row set on wide tables — the `GROUP BY` + `HAVING COUNT(DISTINCT ...)` form typically scales better as the required set grows.
- The double-`NOT EXISTS` form avoids materializing large intermediate joins entirely and tends to perform well even as the required set grows, at the cost of being harder to read.

---

## 🚨 Common Mistakes

- **The classic footgun:** hardcoding `HAVING COUNT(DISTINCT department_id) = 2` and later adding a third department to the `IN (101, 102, 103)` list without also updating the `= 2` to `= 3` — the query silently keeps working but now answers the wrong question (matching *any two* of three required departments, not all three).
- Using `COUNT(department_id)` instead of `COUNT(DISTINCT department_id)` — duplicate assignment rows for the same department would inflate the count and produce false positives.
- Forgetting the `WHERE department_id IN (...)` pre-filter and grouping the entire table, which silently changes the meaning of `COUNT(DISTINCT department_id)` to "how many distinct departments in total," not "how many of the *required* departments."
- Confusing this "match all" pattern with a simple `IN` filter, which only requires matching *any one* value in the set — the two solve fundamentally different problems.
- Assuming the result answers "works *only* in these departments" — the pattern finds employees who touch *at least* all the required departments, and says nothing about whether they also belong to others (see employee 3 in the example data).

---

## 💡 Wisdom from Mímir

`IN` and "match every value in a set" sound like close cousins, but they sit on opposite sides of a very old distinction in logic:
> "Does this row match *something* in my list?" is existential. **"Does this entity match *everything* in my list?" is universal.**

SQL has no built-in "for all" operator — `HAVING COUNT(DISTINCT ...) = N` is how you smuggle universal quantification into a language built around existential row-matching. Whenever you find yourself counting distinct matches against the size of a required set, recognize that you've quietly built a "for all" out of "count how many."

---

## 🔗 Related Notes

- [[Finding Common Records Between Two Tables]]
- [[Finding Duplicate Records]]
- [[Counting Employees in Each Department]]
- [[Find the Total Number of Departments]]
- [[Calculating Multiple Aggregates Per Group]]
