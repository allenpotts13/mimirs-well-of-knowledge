---
type: query-pattern
status: published
created: 2026-08-12
updated: 2026-08-12
technology: SQL
difficulty: Beginner
tags:
  - "null"
  - missing-data
  - relationships
  - filtering
aliases:
  - Employees Without a Manager
  - IS NULL
  - Finding NULL Values
publish: true
permalink: sql/finding-records-with-a-missing-relationship
---

# <span class="rune">ᚲ</span> Finding Records With a Missing Relationship

> *Use `IS NULL` to find rows where an optional relationship or value is missing.*

---

## 🎯 Problem

Find all employees who do not have a manager assigned.

---

## 🤔 Mental Model

If `manager_id` represents the employee's manager, then: 
```text 
manager_id = 42 
``` 

means: 
> This employee has manager 42. 

But: 
```text 
manager_id = NULL 
``` 

means: 
> No manager value is present.

---

## 🧠 Why This Pattern Works

`NULL` represents an unknown or missing value. 

Because `NULL` is not an ordinary value, it cannot be tested using: 
```sql 
= NULL 
``` 

Instead SQL provides:
```sql 
IS NULL 
```

---

## 💻 SQL Solution

```sql
SELECT * 
FROM employees 
WHERE manager_id IS NULL;
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Identify the optional column
```sql 
manager_id 
``` 
### Step 2 — Test for missing values 
```sql 
IS NULL 
``` 
### Step 3 — Return only rows with no value 

These might represent: 
- Executives 
- Unassigned employees 
- Incomplete data 
- Broken relationships 

The business meaning depends on the schema.

--- 
## 🧪 Opposite Pattern 

To return employees who **do** have a manager: 
```sql 
SELECT * 
FROM employees 
WHERE manager_id IS NOT NULL; 
```

---

## 🚀 Common Use Cases

- Employees without managers 
- Orders without shipment dates 
- Accounts without assigned representatives 
- Records awaiting completion 
- Missing optional relationships 
- Data-quality checks

---

## ⚖️ Alternatives

For relationship analysis across tables, a `LEFT JOIN` can be used to detect missing related records:

```sql 
SELECT e.* 
FROM employees e 
LEFT JOIN managers m 
	ON e.manager_id = m.manager_id 
WHERE m.manager_id IS NULL; 
``` 

This answers a slightly different question: 
> Does the related manager record actually exist?

---

## ⚠️ Performance Considerations

- An index may help depending on the database and data distribution. 
- Large numbers of `NULL` values may affect optimizer choices. 
- Relationship validation may require joins rather than simply checking whether the foreign-key column is null.

---

## 🚨 Common Mistakes

### Incorrect
```sql 
WHERE manager_id = NULL 
``` 

### Correct 
```sql 
WHERE manager_id IS NULL 
``` 

Other common mistakes include: 
- Assuming `NULL` means zero. 
- Assuming `NULL` means an empty string. 
- Using `NOT IN` without considering `NULL` behavior. 
- Confusing a missing foreign-key value with a broken foreign-key relationship.

---

## 💡 Wisdom from Mímir

`NULL` does not mean: 
```text 
0 
'' 
false 
``` 

It means: 
> **A value is absent or unknown.** 

Whenever a query involves `NULL`, think carefully about SQL's three-valued logic rather than treating it like an ordinary value. 

---

## 🔗 Related Notes

- [[Filtering Rows With a NULL Column]] — the same IS NULL mechanics applied to non-relationship columns
- [[Finding Employees With No Subordinates]] — a related but distinct question: absence of *incoming* references (nobody reports to them) rather than a missing *outgoing* reference
- [[Finding Common Records Between Two Tables]] — the mirror-image question, finding rows that DO match across tables