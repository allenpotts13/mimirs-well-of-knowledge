---
type: query-pattern
status: published
created: 2026-08-11
updated: 2026-08-11
technology: SQL
difficulty: Intermediate
tags:
  - intersect
  - set-operations
  - comparison
  - tables
aliases:
  - Common Records Between Tables
  - Finding Matching Rows
  - INTERSECT
publish: true
---

# 🧩 Finding Common Records Between Two Tables

> *Use the `INTERSECT` set operator to return rows that exist in the results of both queries.*

---

## 🎯 Problem

Two datasets contain similar information. 

We want to return only the records that appear in **both** result sets.

---

## 🤔 Mental Model

Think of a Venn diagram: 

`INTERSECT` returns only the overlap.

---

## 🧠 Why This Pattern Works

SQL set operators compare the results produced by separate queries. 

`INTERSECT` keeps rows appearing in both result sets. 

Conceptually: 

```text 
Result A ∩ Result B 
```

---

## 💻 SQL Solution

```sql
SELECT employee_id, employee_name 
FROM current_employees 

INTERSECT 

SELECT employee_id, employee_name 
FROM benefit_enrollments;
```

The result contains employees appearing in both datasets.

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Produce the first set 

```sql SELECT employee_id, employee_name FROM current_employees; ```

### Step 2 — Produce the second set
```sql SELECT employee_id, employee_name FROM benefit_enrollments; ``` 

### Step 3 — Find the intersection ```sql INTERSECT ``` Only rows common to both results remain. 
--- 
## 🧪 Example Data 
### current_employees

| employee_id | employee_name |
|---:|---|
| 1 | Alice |
| 2 | Bob |
| 3 | Carol | 

### benefit_enrollments 

| employee_id | employee_name | 
|---:|---| 
| 2 | Bob | 
| 3 | Carol | 
| 4 | David | 

### Result 

| employee_id | employee_name |
|---:|---| 
| 2 | Bob |
| 3 | Carol |

---

## 🚀 Common Use Cases

- Comparing datasets 
- Finding customers appearing in two systems 
- Identifying employees enrolled in a program 
- Comparing staging and production data 
- Data reconciliation 
- Validating migrations

---

## ⚖️ Alternatives

### INNER JOIN 

If the tables have relational keys, an `INNER JOIN` is often more flexible:

```sql 
SELECT 
	e.employee_id, 
	e.employee_name 
FROM current_employees e 
INNER JOIN benefit_enrollments b 
	ON e.employee_id = b.employee_id; 
``` 
### EXISTS 

```sql 
SELECT 
	e.employee_id, 
	e.employee_name 
FROM current_employees e 
WHERE EXISTS 
( 
	SELECT 1 
	FROM benefit_enrollments b 
	WHERE b.employee_id = e.employee_id 
); 
``` 

`EXISTS` is especially useful when you only need to determine whether a matching row exists.

---

## ⚠️ Performance Considerations

- Both queries must be processed before the intersection can be determined. 
- Appropriate indexes can improve comparisons. 
- Selecting unnecessary columns increases comparison work. 
- `INNER JOIN` or `EXISTS` may be preferable when matching by specific keys.

---

## 🚨 Common Mistakes

- Assuming `INTERSECT` behaves like an `INNER JOIN`. 
- Returning different numbers of columns from the two queries. 
- Comparing incompatible data types. 
- Using `SELECT *` when the tables have different structures. 
- Including columns that prevent otherwise related rows from being considered identical.

---

## 💡 Wisdom from Mímir

`INTERSECT` compares **result sets**, while `INNER JOIN` combines **related rows**. 

That distinction determines which tool makes more sense. 

If the question is: 
> "Which complete results appear in both sets?" 

Think `INTERSECT`.

If the question is: 
> "How are these records related?" 

Think `JOIN`.

---

## 🔗 Related Notes

- [[Finding Records That Match Every Value in a Set]] — a related but different question: matching *all* required values instead of finding overlap between two result sets
- [[Finding Records With a Missing Relationship]] — the mirror-image question, finding rows with no match rather than rows with a match