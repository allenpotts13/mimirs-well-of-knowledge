---
type: query-pattern
status: active
created: 2026-08-13
updated: 2026-08-13
technology: SQL
difficulty: Intermediate
tags:
  - query-pattern
  - order-by
  - limit
aliases:
  - First and Last Record
  - SQL First and Last Record
---

# 🧩 How to Fetch the First and Last Record from a Table

> *A reusable SQL solution to retrieve the first and last records from a table.*

---

## 🎯 Problem

How can we retrieve both the first and last record from a table?

For this example, `employee_id` determines the ordering of the records.

---

## 🤔 Mental Model

Think of this as two separate problems:

1. Find the first record.
2. Find the last record.
3. Combine the two results.

We can use `ORDER BY` in opposite directions and take the first row from each result.


---

## 🧠 Why This Pattern Works

`ORDER BY` determines which record appears first.

Ascending:

```sql
ORDER BY employee_id ASC
```

puts the smallest employee ID first.

Descending:

```sql
ORDER BY employee_id DESC
```

puts the largest employee ID first.

`LIMIT 1` then returns only the first record from each result.

`UNION ALL` combines the two results.

---
## 💻 SQL Solution

```sql
(
    SELECT *
    FROM employees
    ORDER BY employee_id ASC
    LIMIT 1
)

UNION ALL

(
    SELECT *
    FROM employees
    ORDER BY employee_id DESC
    LIMIT 1
);
```

---

## 🔄 Step-by-Step Breakdown

1. Sort the employees by `employee_id` in ascending order.

```sql
ORDER BY employee_id ASC
```

2. Return the first record.

```sql
LIMIT 1
```

3. Sort the employees in descending order.

```sql
ORDER BY employee_id DESC
```

4. Return the first record from that result.

5. Combine both records using `UNION ALL`.

The pattern is:

First record

    ↓

ASC + LIMIT 1

  

Last record

    ↓

DESC + LIMIT 1

  

Combine

    ↓

UNION ALL

---

## 🚀 Common Use Cases

- Finding the first and latest transaction
- Finding earliest and most recent records
- Comparing beginning and ending records
- Reporting
- Auditing
- Data validation

---

## ⚖️ Alternatives

If you only need the minimum and maximum IDs rather than the complete records:

```sql
SELECT
    MIN(employee_id) AS first_employee,
    MAX(employee_id) AS last_employee
FROM employees;
```

Window functions can also be useful when more information is needed alongside the ranking.

---

## ⚠️ Performance Considerations

- An index on `employee_id` can make these lookups efficient.
- Sorting an unindexed column may require additional work.
- `LIMIT 1` reduces the number of rows returned.
- Check the execution plan when working with very large tables.

---

## 🚨 Common Mistakes

- Assuming a table has an inherent order.
- Forgetting `ORDER BY`.
- Using `ASC` when the newest/largest value is needed.
- Assuming `employee_id` represents chronological order.
- Using `UNION` when `UNION ALL` is sufficient.

---

## 💡 Wisdom from Mímir

> _"First and last only have meaning when you define what you're ordering by."_

A database table does not guarantee row order unless you explicitly specify it.

---

## 🔗 Related Notes

- [[Retrieving the Last N Records]] — the "last" half of this pattern, generalized to N rows instead of one
- [[Retrieving the Top N Highest Values]] — the same ORDER BY + LIMIT building block, ranking by value instead of by row order
- [[Finding the Youngest Employee]] — a concrete real-world case of taking "the first row" after sorting by a date