---
type: query-pattern
status: published
created: 2026-09-02
updated: 2026-09-02
technology: SQL
difficulty: Intermediate
tags:
  - self-referencing-table
  - anti-join
  - not-in
  - hierarchies
aliases:
  - Employees Who Are Not Managers
  - Finding Leaf Nodes in a Hierarchy
publish: true
permalink: sql/finding-employees-with-no-subordinates
---

# <span class="rune">ᚲ</span> Finding Employees With No Subordinates

> *Use `NOT IN` against the set of all `manager_id` values to find employees nobody reports to.*

---

## 🎯 Problem

Find all employees who do not have any subordinates.

More generally, this pattern answers:
> Which rows in a self-referencing table are never referenced by any other row?

---

## 🤔 Mental Model

Picture an org chart drawn as boxes and arrows, where each arrow points from an employee up to their manager. Some boxes have arrows pointing *into* them (they have subordinates); others have no incoming arrows at all — nobody points up to them. This pattern collects every box with zero incoming arrows: the "leaves" of the tree.

---

## 🧠 Why This Pattern Works

The `employees` table is self-referencing: `manager_id` on one row points to `employee_id` on another row in the same table. The set of everyone who *is* a manager is simply the distinct, non-null values that appear in the `manager_id` column. Anyone whose `employee_id` does **not** appear in that set has no one reporting to them.

`NOT IN` performs exactly that "is this value absent from that set?" check, which is why this is called an **anti-join** pattern — it's the mirror image of `IN`, which asks "is this value present?"

---

## 💻 SQL Solution

```sql
SELECT *
FROM employees
WHERE employee_id NOT IN (
    SELECT manager_id
    FROM employees
    WHERE manager_id IS NOT NULL
);
```

The `WHERE manager_id IS NOT NULL` guard inside the subquery is not optional stylistic polish — it's what keeps `NOT IN` from silently breaking (see Common Mistakes).

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Build the set of everyone who manages someone

```sql
SELECT manager_id
FROM employees
WHERE manager_id IS NOT NULL
```

This collects every distinct `manager_id` that actually appears on at least one row, excluding `NULL` (which represents "this employee has no manager," not "this employee manages nobody").

### Step 2 — Compare each employee against that set

```sql
WHERE employee_id NOT IN (...)
```

For every row in `employees`, MySQL checks whether that row's `employee_id` shows up anywhere in the manager set built in Step 1.

### Step 3 — Keep only the non-matches

```sql
SELECT *
```

Rows whose `employee_id` was never used as anyone's `manager_id` pass the filter — these are the employees with no subordinates.

---

## 🧪 Example Data

| employee_id | name  | manager_id |
| ----------- | ----- | ---------- |
| 1           | Alice | NULL       |
| 2           | Bob   | 1          |
| 3           | Carol | 1          |
| 4           | Dan   | 2          |
| 5           | Erin  | 2          |

Query:
```sql
SELECT *
FROM employees
WHERE employee_id NOT IN (
    SELECT manager_id FROM employees WHERE manager_id IS NOT NULL
);
```

The manager set from the subquery is `{1, 2}` (Alice and Bob). Result:

| employee_id | name  | manager_id |
| ----------- | ----- | ---------- |
| 3           | Carol | 1          |
| 4           | Dan   | 2          |
| 5           | Erin  | 2          |

Carol, Dan, and Erin appear nowhere in the `manager_id` column, so none of them manage anyone.

---

## 🚀 Common Use Cases

- Identifying individual contributors versus people managers in an org chart
- Finding "leaf" categories in a self-referencing category/subcategory table
- Locating terminal nodes in any parent-child hierarchy (folder trees, bill-of-materials, org structures)
- Auditing which staff are eligible for a "no direct reports" policy or benefit
- Detecting orphaned or unreferenced rows in any self-join scenario

---

## ⚖️ Alternatives

### LEFT JOIN / IS NULL anti-join

```sql
SELECT e.*
FROM employees e
LEFT JOIN employees m ON e.employee_id = m.manager_id
WHERE m.manager_id IS NULL;
```

This joins each employee against anyone who lists them as a manager; when no such match exists, the joined columns come back `NULL`. This form sidesteps the `NOT IN` + `NULL` pitfall entirely and is often the optimizer-friendlier choice on large tables.

### NOT EXISTS

```sql
SELECT e.*
FROM employees e
WHERE NOT EXISTS (
    SELECT 1
    FROM employees s
    WHERE s.manager_id = e.employee_id
);
```

`NOT EXISTS` is correlated per row and, unlike `NOT IN`, is unaffected by `NULL` values inside the subquery — it only cares whether a matching row exists, so it doesn't need the extra `IS NOT NULL` guard.

### Aggregation with a COUNT

```sql
SELECT e.*
FROM employees e
LEFT JOIN employees s ON s.manager_id = e.employee_id
GROUP BY e.employee_id
HAVING COUNT(s.employee_id) = 0;
```

This counts subordinates per employee explicitly, which is useful if you also want the subordinate count displayed for employees who *do* have reports, not just a yes/no filter.

---

## ⚠️ Performance Considerations

- `NOT IN` subqueries can be slower than `NOT EXISTS` or `LEFT JOIN … IS NULL` on large tables because some MySQL versions execute them less efficiently, particularly without a helpful index.
- An index on `manager_id` (and `employee_id`, typically already the primary key) is important for good performance on large hierarchies.
- `NOT EXISTS` and `LEFT JOIN … IS NULL` are generally preferred in production for this exact reason — they tend to produce better execution plans and don't carry the `NULL` correctness risk.
- Very deep or wide hierarchies (many thousands of employees) benefit from `EXPLAIN`-checking whichever variant you choose before shipping it.

---

## 🚨 Common Mistakes

- Omitting `WHERE manager_id IS NOT NULL` in the subquery — if even one row has a `NULL` `manager_id`, the entire `NOT IN` comparison silently returns **zero rows**, because SQL cannot determine `employee_id <> NULL` is true or false.
- Using `IN` when the intent is "does not manage anyone" — this inverts the entire result set.
- Forgetting that a `manager_id` of `NULL` means "this employee has no manager," not "this employee has no subordinates" — the two are unrelated facts about the same row.
- Assuming self-joins always need aliasing conventions like `e`/`m` — inconsistent aliasing across a query is a frequent source of confusing bugs in self-referencing tables.
- Not testing the query against a hierarchy that actually contains `NULL` manager IDs before trusting it in production.

---

## 💡 Wisdom from Mímir

`NOT IN` looks like the natural opposite of `IN`, but SQL's three-valued logic makes negation dangerous the moment `NULL` enters a set. The real question is never:
> "Who is absent from this list?"

It's:
> **Can this list even contain an unknown value — and if so, have I accounted for what "unknown" does to a comparison?**

`NOT EXISTS` sidesteps the question by never asking it in the first place.

---

## 🔗 Related Notes

- [[Find All Employees Who Report to a Specific Manager]]
- [[Finding Records With a Missing Relationship]]
- [[Finding Peers Who Share the Same Manager]]
- [[Finding Employees Who Earn More Than Their Manager]]
- [[Comparing Related Rows With a Self-Join]]
