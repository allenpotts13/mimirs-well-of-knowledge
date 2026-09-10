---
type: query-pattern
status: published
created: 2026-08-12
updated: 2026-08-12
technology: SQL
difficulty: Beginner
tags:
  - like
  - pattern-matching
  - strings
  - filtering
aliases:
  - Names Starting With A
  - LIKE Prefix Search
publish: true
---

# 🧩 Finding Values That Start With a Pattern

> *Use `LIKE` with the `%` wildcard to return text values that begin with a specified sequence.*

---

## 🎯 Problem

Find employees whose names begin with the letter `A`.

---

## 🤔 Mental Model

The pattern: 
```text 
A% 
``` 

means: 
> Start with `A`, then allow anything after it. The `%` wildcard represents zero or more characters.

---

## 🧠 Why This Pattern Works

`LIKE` performs pattern matching against text values. 

The pattern:
```sql 
'A%' 
``` 

matches: 
```text 
Allen 
Amanda 
A 
Andrew 
``` 

but does not match:
```text 
Brian 
Sarah 
```

---

## 💻 SQL Solution

```sql
SELECT * 
FROM employees 
WHERE name LIKE 'A%';
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Choose the column 

```sql 
name 
``` 

### Step 2 — Use LIKE

```sql 
LIKE 
``` 

### Step 3 — Define the prefix pattern

```sql 
'A%' 
``` 

`A` must appear first.

`%` allows any number of characters afterward.

--- 
## 🧪 Common Pattern Variations 

### Starts with A 
```sql 
WHERE name LIKE 'A%' 
``` 
### Ends with A 
```sql 
WHERE name LIKE '%A' 
``` 
### Contains A 
```sql 
WHERE name LIKE '%A%' 
``` 
### Second character is A 
```sql 
WHERE name LIKE '_A%' 
``` 

The `_` wildcard represents exactly one character.

---

## 🚀 Common Use Cases

- Searching names by prefix 
- Product lookup 
- Filtering codes 
- Searching email domains 
- Matching standardized identifiers 
- User-facing search boxes

---

## ⚖️ Alternatives

### PostgreSQL case-insensitive matching
```sql 
WHERE name ILIKE 'a%' 
``` 
### Explicit normalization 
```sql 
WHERE LOWER(name) LIKE 'a%' 
``` 

This can provide consistent case-insensitive behavior, though performance should be considered.

---

## ⚠️ Performance Considerations

A prefix search such as:
```sql 
LIKE 'A%' 
``` 

may be index-friendly depending on the database and collation. 

A leading wildcard: 
```sql 
LIKE '%A' 
``` 

or 

```sql 
LIKE '%A%' 
``` 

often prevents efficient use of a traditional index.

---

## 🚨 Common Mistakes

- Forgetting `%`. 
- Confusing `%` with `_`. 
- Assuming `LIKE` is always case-sensitive. 
- Using a leading wildcard on very large datasets without understanding the performance impact. 
- Failing to escape literal wildcard characters when needed.

---

## 💡 Wisdom from Mímir

Where the wildcard appears matters.

```text 
A% Prefix search 
%A Suffix search 
%A% Contains search 
``` 

The pattern changes both the meaning and potentially the performance of the query.

---

## 🔗 Related Notes

- [[Matching Text Patterns with LIKE]] — the mirror-image case, matching a suffix instead of a prefix
- [[Filtering Rows by Email Domain]] — a real-world application of the same trailing-wildcard technique
- [[Comparing the First and Last Character of a String]] — a different string-position technique (LEFT/RIGHT) for a related class of question