---
type: query-pattern
status: published
created: 2026-08-13
updated: 2026-08-13
technology: SQL
difficulty: Intermediate
tags:
  - update
  - calculations
  - data-modification
  - salary
aliases:
publish: true
permalink: sql/increasing-every-employees-salary-by-10
---

# <span class="rune">ᚲ</span> Increasing Every Employee's Salary by 10%

> *Use an `UPDATE` statement with an arithmetic expression to modify numeric values by a percentage.*

---

## 🎯 Problem

Increase the salary of every employee by 10%.

---

## 🤔 Mental Model

A 10% increase means:

```text
Original × 1.10
```

So:

```text
50000 × 1.10 = 55000
```

---

## 🧠 Why This Pattern Works

Explain the logic.

Don't just explain the SQL.

Explain the thinking.

---

## 💻 SQL Solution

```sql
UPDATE employees
SET salary = salary * 1.10;
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Choose the table

```sql
UPDATE employees
```

### Step 2 — Specify the column to change

```sql
SET salary =
```

### Step 3 — Calculate the new value

```sql
salary * 1.10
```

The existing salary becomes the basis for the calculation.

---

## 🧪 Example

Before:

| Employee | Salary |
|---|---:|
| Alice | $50,000 |
| Bob | $60,000 |

After:

| Employee | Salary |
|---|---:|
| Alice | $55,000 |
| Bob | $66,000 |

---

## ⚠️ Critical Safety Rule

This query affects **every row** because there is no `WHERE` clause.

```sql
UPDATE employees
SET salary = salary * 1.10;
```

Before executing an `UPDATE`, always ask:

> **Which rows should change?**

If only certain employees should receive the increase:

```sql
UPDATE employees
SET salary = salary * 1.10
WHERE department_id = 10;
```

---

## 🛡️ Safe Workflow

Before running a destructive or modifying query, first preview the affected rows:

```sql
SELECT *
FROM employees
WHERE department_id = 10;
```

Then perform the update:

```sql
UPDATE employees
SET salary = salary * 1.10
WHERE department_id = 10;
```

When supported by the database, transactions can provide another layer of protection:

```sql
BEGIN;

UPDATE employees
SET salary = salary * 1.10
WHERE department_id = 10;

-- Verify results

COMMIT;
```

If something is wrong:

```sql
ROLLBACK;
```


---

## 🚀 Common Use Cases

- Salary increases
- Price adjustments
- Tax calculations
- Percentage-based discounts
- Rate adjustments
- Bulk data corrections

---

## ⚖️ Alternatives

```sql
SELECT
    employee_id,
    salary,
    salary * 1.10 AS new_salary
FROM employees;
```

This is useful for verifying the calculation **before actually modifying the data**.

You can also limit the update:

```sql
UPDATE employees
SET salary = salary * 1.10
WHERE department_id = 10;
```

---

## ⚠️ Performance Considerations

- An `UPDATE` modifies every qualifying row.
- Large updates can generate substantial transaction log activity.
- Updating indexed columns can require additional index maintenance.
- Large production updates may need to be performed in batches.
- Transactions should be used when appropriate so changes can be verified or rolled back.

And I'd keep the **"SELECT before UPDATE"** section because that's a very valuable professional habit.

---

## 🚨 Common Mistakes

### Forgetting the WHERE clause

```sql
UPDATE employees
SET salary = salary * 1.10;
```

may update the entire table.

### Using the wrong multiplier

```text
+10% → × 1.10
+20% → × 1.20
-10% → × 0.90
```

### Running an update without first verifying the target rows.

---

## 💡 Wisdom from Mímir

> **SELECT before UPDATE.**

If you're not completely certain which rows an `UPDATE` will affect, write the equivalent `SELECT` first.

A missing `WHERE` clause can turn a five-second correction into a major incident.

---

## 🔗 Related Notes

- [[Updating Values Conditionally with CASE]] — the same UPDATE...SET arithmetic idea, varying the multiplier per department instead of applying one flat rate
- [[Delete All Employees from a Department]] — shares the same "SELECT before you mutate" safety discipline for a WHERE-less/destructive statement