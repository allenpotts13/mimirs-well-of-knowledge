---
type: concept
status: active
created: 2026-09-10
updated: 2026-09-10
technology: SQL
difficulty: Beginner
tags:
  - keys
  - constraints
  - relational-database
aliases:
  - Primary Key
  - PK
---

# 📚 Primary Keys

> The column (or columns) that uniquely identifies every row in a table.

---

## 🎯 Purpose

A primary key guarantees that every row can be uniquely and reliably referenced — by the database, by other tables, and by application code.

Every table that will ever be joined to or updated by ID needs one.

---

## 🧠 Key Ideas

- Must be unique across every row and can never be NULL.
- A table can have only one primary key, but that key can span multiple columns (a **composite key**).
- Almost always automatically indexed by the database engine.
- Commonly implemented as an auto-incrementing integer (`IDENTITY`, `SERIAL`, `AUTO_INCREMENT`) or a UUID.
- Foreign keys in other tables reference a primary key to form relationships — see [[Foreign Keys]].

---

## ⚙️ How It Works

The database enforces two rules on the primary key column(s) automatically: uniqueness and non-nullability. Any `INSERT` or `UPDATE` that would violate either is rejected.

Because it's indexed by default, looking up a row by primary key is typically the fastest possible lookup in the table.

---

## 💻 Examples

```sql
CREATE TABLE Customers (
	CustomerID INT PRIMARY KEY IDENTITY(1,1),
	CustomerName VARCHAR(100) NOT NULL
);

-- Composite primary key
CREATE TABLE OrderItems (
	OrderID INT,
	ProductID INT,
	Quantity INT,
	PRIMARY KEY (OrderID, ProductID)
);
```

---

## 🚀 Real World Applications

- Uniquely identifying a customer, order, or product row
- The join target for every foreign key relationship in the schema
- Safe row-level references in application code (URLs, API responses, logs)

---

## ⚖️ Advantages

- Guarantees no duplicate or ambiguous rows.
- Automatically indexed, so lookups by ID are fast.
- Gives every row a stable identity independent of its other column values.

---

## ⚠️ Limitations

- A poorly chosen natural key (e.g. email address, SSN) can change over time — surrogate keys (auto-incrementing IDs) avoid that problem.
- Composite keys make foreign key references in child tables more verbose.

---

## 🚨 Common Mistakes

- Using a "meaningful" business value (email, username) as the primary key, then needing to change it later.
- Forgetting a table needs a primary key at all, making duplicate rows possible.
- Assuming primary key order reflects insertion order — it doesn't, unless explicitly sorted.

---

## 📖 Further Reading

Documentation

Books

Videos

Articles

---

## 💡 Wisdom from Mímir

Default to a surrogate key (auto-incrementing ID) unless there's a strong reason not to. Business data changes — a customer's email updates, a product's SKU gets revised — and a primary key that has to change is a primary key that breaks every foreign key pointing at it.

---

## 🔗 Related Notes

- [[Foreign Keys]]
- [[Constraints]]
- [[Indexes]]
- [[INNER JOIN]]
