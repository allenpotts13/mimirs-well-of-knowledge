---
type: query-pattern
status: active
created: 2026-08-11
updated: 2026-08-11
technology: SQL
difficulty: Intermediate
tags:
  - duplicates
  - row-number
  - window-functions
  - cte
  - data-cleaning
aliases:
  - Delete Duplicate Records
  - Removing Duplicate Rows
---

# 🧩 Deleting Duplicate Rows

> *Identify duplicate records with `ROW_NUMBER()` and remove the unwanted copies while preserving one record from each duplicate group.*

---

## 🎯 Problem

A table contains multiple rows representing the same logical record. 

We need to: 
1. Define what makes a row a duplicate. 
2. Determine which copy should be kept. 
3. Delete only the unwanted copies.

---

## 🤔 Mental Model

Imagine placing identical records into groups.

Within each group, number the records:

| Record | Row Number |
|---|---:|
| Original | 1 |
| Duplicate | 2 |
| Duplicate | 3 |

Keep `1`. 

Delete everything where: 
```sql 
row_num > 1 
``` 

`ROW_NUMBER()` gives us a controlled way to decide which record survives.

---

## 🧠 Why This Pattern Works

`PARTITION BY` defines what constitutes a duplicate. 

`ROW_NUMBER()` assigns a unique sequence number to every row within each duplicate group. 

The `ORDER BY` inside the window function determines which row becomes `1` and therefore which record is preserved. 

Rows numbered greater than `1` are duplicates that can be removed.

---

## 💻 SQL Solution

```sql
WITH DuplicateRows AS 
( 
	SELECT 
		*, 
		ROW_NUMBER() OVER ( 
			PARTITION BY email 
			ORDER BY customer_id 
		) AS row_num 
	FROM customers 
) 
DELETE FROM DuplicateRows 
WHERE row_num > 1;
```

This example considers matching `email` values duplicates and preserves the row with the lowest `customer_id`.

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Define the duplicate 

```sql 
PARTITION BY email 
``` 

Every row containing the same email belongs to the same group.
### Step 2 — Decide which row survives 

```sql 
ORDER BY customer_id 
``` 

The lowest `customer_id` receives:  

```text 
row_num = 1 
``` 
### Step 3 — Number the duplicates 

```sql 
ROW_NUMBER() OVER ( 
	PARTITION BY email 
	ORDER BY customer_id 
) 
``` 

Example: 

| customer_id | email | row_num |
|---:|---|---:|
| 10 | alice@example.com | 1 |
| 14 | alice@example.com | 2 |
| 19 | alice@example.com | 3 |
### Step 4 — Delete everything except the survivor

```sql 
WHERE row_num > 1 
``` 

Rows `14` and `19` are removed while row `10` remains. 

## 🧪 Safe Preview Before Deleting 

Before executing a `DELETE`, run the logic as a `SELECT`: 

```sql 
WITH DuplicateRows AS 
( 
	SELECT 
		*, 
		ROW_NUMBER() OVER ( 
			PARTITION BY email 
			ORDER BY customer_id 
		) AS row_num 
	FROM customers
) 
SELECT * 
FROM DuplicateRows 
WHERE row_num > 1; 
``` 

Inspect these results before deleting anything.

---

## 🚀 Common Use Cases

- Cleaning imported datasets 
- Removing duplicate customer accounts 
- Deduplicating staging tables 
- Cleaning repeated transactions 
- Correcting ETL ingestion problems 
- Preparing data before adding a unique constraint

---

## ⚖️ Alternatives

The exact deletion syntax varies by database. 

PostgreSQL, MySQL, Oracle, and SQL Server do not necessarily support identical CTE deletion behavior. 

Another approach is to identify duplicate primary keys first and then delete from the underlying table. 

The correct method should therefore consider the SQL dialect being used.

---

## ⚠️ Performance Considerations

- `ROW_NUMBER()` may require sorting a large number of records. 
- Index columns used by `PARTITION BY` and `ORDER BY` when appropriate. 
- Filter the dataset before ranking if only part of the table requires cleanup. 
- Large deletions may create significant transaction-log activity. 
- Consider deleting large volumes in controlled batches.

---

## 🚨 Common Mistakes

- Running the `DELETE` before previewing the affected records. 
- Failing to define exactly what constitutes a duplicate. 
- Using an arbitrary `ORDER BY` and accidentally keeping the wrong record. 
- Partitioning on too few columns. 
- Assuming this exact syntax works identically across every database. 
- Deleting duplicates without fixing the process that created them.

---

## 💡 Wisdom from Mímir

**Finding duplicates and deleting duplicates are two different operations.** 

Never begin with the deletion. 

First determine: 
- Why are these records considered duplicates? 
- Which record should survive? 
- Does the surviving record contain the best data? 
- Why were duplicates allowed in the first place? 

Always preview the rows selected for deletion before modifying production data. 

After cleanup, consider whether a `UNIQUE` constraint or upstream validation should prevent the duplicates from returning.

---

## 🔗 Related Notes

- [[Finding Duplicate Records]] — identify duplicates first; this note is the cleanup step that follows it
- [[Ranking the Top N Rows Within Each Group]] — the same PARTITION BY + ranking-window technique, used to keep top rows instead of discarding extra ones