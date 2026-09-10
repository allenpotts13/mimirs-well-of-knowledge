---
type: query-pattern
status: published
created: 2026-08-02
updated: 2026-08-02
technology: SQL
difficulty: Beginner
tags:
  - duplicates
  - group-by
  - having
  - data-quality
aliases:
  - Finding Duplicate Records
  - Identifying Duplicate Values
publish: true
permalink: sql/finding-duplicate-records
---

# <span class="rune">ᚲ</span> Finding Duplicate Records

> *Identify values or combinations of values that occur more than once in a table.*

---

## 🎯 Problem

Find duplicate values in a column by grouping equal values and returning only groups with more than one row.

---

## 🤔 Mental Model

Imagine sorting customer email addresses into labeled boxes. 

Every occurrence of the same email goes into the same box.

After counting each box, any box containing more than one item represents a duplicate.

---

## 🧠 Why This Pattern Works

`GROUP BY` combines rows that share the same value. 

`COUNT(*)` measures the number of rows in each group. 

`HAVING` filters the grouped results after aggregation and keeps only groups whose count is greater than one.

---

## 💻 SQL Solution

```sql
SELECT 
	email, 
	COUNT(*) AS occurrence_count 
FROM customers 
GROUP BY email 
HAVING COUNT(*) > 1;
```

---

## 🔄 Step-by-Step Breakdown

### Step 1: Group matching values 

```sql 
GROUP BY email
``` 

All identical email addresses are placed into the same logical group. 

### Step 2: Count each group 

```sql 
COUNT(*) 
``` 

The database counts how many rows belong to each email address. 

### Step 3: Keep duplicate groups

```sql 
HAVING COUNT(*) > 1 
``` 

Groups containing only one record are removed.

## 🧪 Example Data 

| customer_id | email | 
|---:|---| 
| 1 | alice@example.com |
| 2 | bob@example.com | 
| 3 | alice@example.com | 
| 4 | carol@example.com | 
| 5 | bob@example.com |

Result: 

| email | occurrence_count |
|---|---:|
| alice@example.com | 2 |
| bob@example.com | 2 |

## 🧩 Finding Duplicates Across Multiple Columns 

Sometimes a duplicate is defined by a combination of fields:

```sql 
SELECT 
	first_name, 
	last_name, 
	birth_date, 
	COUNT(*) AS occurrence_count 
FROM customers 
GROUP BY 
	first_name, 
	last_name, 
	birth_date 
HAVING COUNT(*) > 1; 
``` 

This identifies repeated combinations rather than duplicate values from only one column.

## 🔎 Returning the Full Duplicate Rows 

The grouped query identifies which values are duplicated, but it does not return every original row. 

To retrieve the full records: 

```sql 
SELECT c.* 
FROM customers c 
INNER JOIN 
( 
	SELECT email 
	FROM customers 
	GROUP BY email 
	HAVING COUNT(*) > 1 
) AS duplicates 
	ON c.email = duplicates.email 
ORDER BY c.email; 
```


---

## 🚀 Common Use Cases

- Duplicate customer accounts 
- Repeated email addresses 
- Duplicate imported records 
- Repeated transactions 
- Data-quality validation 
- Detecting missing unique constraints

---

## ⚖️ Alternatives

### Window-function approach

```sql 
SELECT * 
FROM 
( 
	SELECT 
		c.*, 
		COUNT(*) OVER ( 
			PARTITION BY email 
		) AS occurrence_count 
	FROM customers c
) AS counted_customers 
WHERE occurrence_count > 1; 
``` 

This returns the original rows while also showing the duplicate count. 

### `ROW_NUMBER()` for duplicate cleanup 

```sql 
SELECT * 
FROM 
( 
	SELECT 
		c.*, 
		ROW_NUMBER() OVER ( 
			PARTITION BY email 
			ORDER BY customer_id 
		) AS row_number 
	FROM customers c 
) AS numbered_customers 
WHERE row_number > 1; 
``` 

This identifies all but the first row from each duplicate group.

---

## ⚠️ Performance Considerations

- Grouping a large table can be expensive. 
- Indexes on the duplicate-checking columns may help. 
- Normalize text before comparison when capitalization or spaces should be ignored. 
- Filter the dataset before grouping when only a subset matters. 
- Composite duplicate checks may benefit from composite indexes.

---

## 🚨 Common Mistakes

- Using `WHERE COUNT(*) > 1` instead of `HAVING COUNT(*) > 1`. 
- Assuming the grouped query returns the complete duplicate rows. 
- Checking only one column when duplicates are defined by several columns. 
- Ignoring differences in capitalization or whitespace. 
- Treating multiple `NULL` values as ordinary duplicates without considering database behavior. 
- Deleting duplicates before determining which row should be retained.

---

## 💡 Wisdom from Mímir

Finding duplicate values and safely removing duplicate rows are separate problems. 

Before deleting anything, determine: 
- What columns define a duplicate? 
- Which record should survive? 
- Are the records truly duplicates or merely similar? 
- Is a unique constraint needed to prevent the problem from returning?

Always run the duplicate-identification query first and inspect the results before writing a deletion query.

---

## 🔗 Related Notes

- [[Deleting Duplicate Rows]] — the natural next step once duplicates have been identified
- [[Finding Groups Below a Size Threshold]] — the same GROUP BY + HAVING shape, filtering on group size in general rather than specifically "> 1"
- [[Finding the Group With the Highest Count]] — another GROUP BY + COUNT pattern, ranking groups instead of flagging them