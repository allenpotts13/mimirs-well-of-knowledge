---
type: query-pattern
status: published
created: 2026-09-02
updated: 2026-09-02
technology: SQL
difficulty: Intermediate
tags:
  - case-expression
  - update
  - conditional-logic
  - salary-adjustment
aliases:
  - Conditional UPDATE with CASE
  - Give Different Raises by Department
publish: true
---

# 🧩 Updating Values Conditionally with CASE

> *Use a `CASE` expression inside `UPDATE ... SET` to apply different values to different rows in a single statement.*

---

## 🎯 Problem

Update salaries of employees based on their department: department 101 gets a 10% raise, department 102 gets a 5% raise, and every other department is left unchanged — all in one statement.

More generally, this pattern answers:
> How do I apply different update logic to different rows without running a separate statement for each case?

---

## 🤔 Mental Model

Picture a sorting attendant standing beside every row as the `UPDATE` passes through the table. For each row, the attendant asks a series of yes/no questions — "Is this department 101? Is it 102?" — and applies whichever rule matches first. If none of the questions get a "yes," the attendant shrugs and leaves the value exactly as it was.

That shrug is the `ELSE` branch, and it matters as much as the rules that do match.

---

## 🧠 Why This Pattern Works

`CASE` is an expression, not a statement — it evaluates to a single value, which means it can be dropped anywhere a value is expected, including on the right-hand side of `SET`. MySQL evaluates the `WHEN` conditions top to bottom, in order, for every row the `UPDATE` touches, and returns the result of the first matching branch.

Because `UPDATE` visits every qualifying row exactly once, a `CASE` expression lets that single pass make a different decision for each row instead of applying one uniform rule — turning what would otherwise be several separate `UPDATE` statements into one atomic operation.

---

## 💻 SQL Solution

```sql
UPDATE employees
SET salary = CASE
  WHEN department_id = 101 THEN salary * 1.10
  WHEN department_id = 102 THEN salary * 1.05
  ELSE salary
END;
```

The `ELSE salary` branch is not decoration — it tells MySQL to leave the column exactly as it already is for every row that matches none of the `WHEN` conditions.

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Check the first condition

```sql
WHEN department_id = 101 THEN salary * 1.10
```

For each row, MySQL checks whether `department_id` equals 101. If so, the new salary becomes 110% of the current salary, and evaluation for that row stops here.

### Step 2 — Check the second condition

```sql
WHEN department_id = 102 THEN salary * 1.05
```

Rows that didn't match department 101 are checked against department 102. A match sets the new salary to 105% of the current value.

### Step 3 — Fall through to ELSE

```sql
ELSE salary
```

Any row that matched neither `WHEN` falls into `ELSE`, which simply returns the row's own current `salary` — an update that changes nothing.

### Step 4 — Apply the computed value

```sql
SET salary = CASE ... END
```

Whatever the `CASE` expression evaluated to becomes the new value written into `salary` for that row, and this repeats for every row `UPDATE` touches.

---

## 🧪 Example Data

| employee_id | department_id | salary (before) |
| ----------- | -------------- | ---------------: |
| 1           | 101            |            50000 |
| 2           | 102            |            60000 |
| 3           | 103            |            55000 |

Query:
```sql
UPDATE employees
SET salary = CASE
  WHEN department_id = 101 THEN salary * 1.10
  WHEN department_id = 102 THEN salary * 1.05
  ELSE salary
END;
```

Result:

| employee_id | department_id | salary (after) |
| ----------- | -------------- | --------------: |
| 1           | 101            |         55000.0 |
| 2           | 102            |         63000.0 |
| 3           | 103            |            55000 |

Employee 3's department isn't mentioned in any `WHEN`, so `ELSE salary` preserves the original 55000 unchanged.

---

## 🚀 Common Use Cases

- Giving different departments, tiers, or job roles different raise percentages in one pass
- Applying tiered discounts or pricing adjustments based on a product category
- Setting a status flag differently depending on which threshold a row crosses
- Bulk-correcting data where the correction depends on the row's current category
- Running a one-time bonus calculation that varies by performance band
- Migrating a column's values according to a lookup-style set of rules

---

## ⚖️ Alternatives

### Three separate UPDATE statements

```sql
UPDATE employees SET salary = salary * 1.10 WHERE department_id = 101;
UPDATE employees SET salary = salary * 1.05 WHERE department_id = 102;
```

This produces the same end result but as three (here, two) separate passes over the table instead of one. Each statement is its own transaction unless explicitly wrapped together, so a failure partway through can leave the raises half-applied — the `CASE` version either updates the whole table consistently or not at all.

### Preview with SELECT before committing

```sql
SELECT
  employee_id,
  department_id,
  salary AS current_salary,
  CASE
    WHEN department_id = 101 THEN salary * 1.10
    WHEN department_id = 102 THEN salary * 1.05
    ELSE salary
  END AS new_salary
FROM employees;
```

Running the identical `CASE` logic inside a `SELECT` first lets you eyeball the exact values an `UPDATE` would write, before running anything destructive.

---

## ⚠️ Performance Considerations

- With no `WHERE` clause, this `UPDATE` rewrites every row in the table — including rows in the `ELSE` branch that don't actually change value — which can generate unnecessary write and replication overhead on large tables.
- Adding `WHERE department_id IN (101, 102)` restricts the statement to only the rows that will actually change, which is faster and makes the `ELSE` branch unnecessary.
- Large `UPDATE` statements can hold row or table locks for their duration; consider batching on very large tables.
- Always test the `CASE` logic with a `SELECT` first — an `UPDATE` mistake is much more expensive to undo than a `SELECT` mistake.

---

## 🚨 Common Mistakes

- Omitting the `ELSE` branch, which causes MySQL to set the column to `NULL` for every row that matches no `WHEN` condition — silently wiping out data for entire departments.
- Running the three-statement alternative without a transaction, risking a half-applied set of raises if the script fails midway.
- Mixing up a percentage multiplier (`* 1.10`) with a flat literal (`= 10`), accidentally setting salaries to a fixed number instead of increasing them.
- Forgetting to test with a `SELECT ... CASE` preview before running the real `UPDATE`.
- Assuming `CASE` conditions are evaluated independently rather than in order — later `WHEN` clauses never run once an earlier one has matched.

---

## 💡 Wisdom from Mímir

The question that matters most isn't:
> "What should change?"

It's:
> **What happens to the rows I didn't think to mention?**

`ELSE` is where that question gets answered. Leave it out, and every row your rules forgot about gets rewritten anyway — just not the way you intended.

---

## 🔗 Related Notes

- [[Increasing Every Employee's Salary by 10%]]
- [[Bucketing Values Into Ranges With CASE]]
- [[Delete All Employees from a Department]]
- [[Calculating Each Group's Percentage of the Total]]
