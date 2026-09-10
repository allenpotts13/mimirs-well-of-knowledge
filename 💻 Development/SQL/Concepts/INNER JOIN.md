---
type: concept
status: active
created: 2026-08-02
updated: 2026-08-02
technology: SQL
difficulty: Beginner
tags:
  - joins
  - relational-database
aliases:
  - SQL INNER JOIN
---
---
# 📚 INNER JOIN

> Returns only rows that have matching values in both tables.

---

## 🎯 Purpose

INNER JOIN combines related data from two or more tables by matching values in a common column.

Use it when only matching records should be returned.

---

## 🧠 Key Ideas

- Returns matching rows only. 
- Requires a relationship between tables. 
- Usually joins on primary and foreign keys. 
- Most common SQL join.

---

## ⚙️ How It Works

The database compares the join columns from each table. 

If a match exists, the combined row is returned. 

If no match exists, that row is excluded from the results. 

Think of it as finding the overlap between two sets.

---

## 💻 Examples

```sql 
SELECT 
	c.CustomerName, 
	o.OrderDate 
FROM Customers c 
INNER JOIN Orders o 
	ON c.CustomerID = o.CustomerID; 
```

---

## 🚀 Real World Applications

- Customer orders 
- Employee departments 
- Student enrollments 
- Inventory transactions

---

## ⚖️ Advantages

- Fast when indexes exist. 
- Easy to understand. 
- Eliminates unmatched rows.

---

## ⚠️ Limitations

- Unmatched data is discarded. 
- Requires a relationship between tables. 

---

## 🚨 Common Mistakes

- Forgetting the ON clause. 
- Joining on the wrong column. 
- Creating duplicate rows with one-to-many relationships.

---

## 📖 Further Reading

Documentation

Books

Videos

Articles

---

## 💡 Wisdom from Mímir

I almost always start with INNER JOIN while developing queries.

If expected rows disappear, that's usually a clue that the relationship isn't what I assumed—not necessarily that the query is wrong.

---

## 🔗 Related Notes

- [[LEFT JOIN]] 
- [[Primary Keys]] 
- [[Foreign Keys]] 
- [[Indexes]]