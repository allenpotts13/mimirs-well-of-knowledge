---
type: concept
status: active
created: 2026-09-10
updated: 2026-09-10
technology: SQL
difficulty: Beginner
tags:
  - fundamentals
  - sql-syntax
aliases:
  - DDL
  - DML
  - DCL
  - TCL
  - SQL Statement Categories
---

# 📚 DDL vs DML vs DCL vs TCL

> Every SQL statement falls into one of four categories, based on what kind of thing it changes — structure, data, permissions, or transaction state.

---

## 🎯 Purpose

Knowing which category a statement belongs to explains its behavior — whether it auto-commits, what it affects, and who's typically allowed to run it.

---

## 🧠 Key Ideas

| Category | Stands for | Changes | Example statements |
|---|---|---|---|
| **DDL** | Data Definition Language | Schema/structure | `CREATE`, `ALTER`, `DROP`, `TRUNCATE` |
| **DML** | Data Manipulation Language | Data/rows | `SELECT`, `INSERT`, `UPDATE`, `DELETE` |
| **DCL** | Data Control Language | Permissions | `GRANT`, `REVOKE` |
| **TCL** | Transaction Control Language | Transaction state | `BEGIN`, `COMMIT`, `ROLLBACK`, `SAVEPOINT` |

- DDL statements typically **auto-commit** immediately in most databases — they can't be rolled back inside a normal transaction the way DML can.
- `TRUNCATE` is technically DDL (it resets the table structure/storage), which is why it can't be rolled back the way `DELETE` (DML) usually can.
- DML changes are exactly what [[Transactions]] wrap and protect.

---

## ⚙️ How It Works

Each category maps to a different layer of what a database does: DDL shapes the containers, DML fills and changes what's inside them, DCL decides who's allowed to touch either, and TCL controls whether a batch of DML changes becomes permanent.

---

## 💻 Examples

```sql
-- DDL: define structure
CREATE TABLE Products (ProductID INT PRIMARY KEY, Name VARCHAR(100));
ALTER TABLE Products ADD Price DECIMAL(10,2);

-- DML: manipulate data
INSERT INTO Products (ProductID, Name, Price) VALUES (1, 'Widget', 9.99);
UPDATE Products SET Price = 12.99 WHERE ProductID = 1;

-- DCL: control access
GRANT SELECT ON Products TO ReportingUser;
REVOKE INSERT ON Products FROM ReportingUser;

-- TCL: control transaction state
BEGIN TRANSACTION;
DELETE FROM Products WHERE ProductID = 1;
ROLLBACK;  -- undoes the delete above
```

---

## 🚀 Real World Applications

- Migrations are almost entirely DDL.
- Everyday application queries are almost entirely DML.
- Setting up role-based access for a reporting user or a read-only replica is DCL.
- Wrapping a multi-step operation safely is TCL, paired with DML.

---

## ⚖️ Advantages

- The category names give an instant signal about blast radius and risk — DDL changes structure for everyone, DML changes specific rows.
- Understanding the split explains why `TRUNCATE` behaves differently from `DELETE` inside a transaction.

---

## ⚠️ Limitations

- Not every engine treats DDL auto-commit behavior identically — always verify on the specific database in use before assuming a DDL statement can be rolled back.

---

## 🚨 Common Mistakes

- Assuming `TRUNCATE` can be rolled back like `DELETE` — on many engines it can't, since it's DDL, not DML.
- Running a DDL statement (like `ALTER TABLE`) inside a transaction and expecting to cleanly roll it back on some engines that auto-commit DDL immediately.
- Confusing `DELETE` (DML, row-by-row, logged, rollback-able) with `TRUNCATE` (DDL, resets the whole table, minimally logged).

---

## 📖 Further Reading

Documentation

Books

Videos

Articles

---

## 💡 Wisdom from Mímir

Before running `TRUNCATE` or any DDL statement against production data, double-check the engine's rollback behavior specifically — the assumption that "it's inside a transaction, so it's safe" doesn't always hold for DDL the way it does for DML.

---

## 🔗 Related Notes

- [[Transactions]]
- [[Emptying a Table with TRUNCATE]]
- [[Constraints]]
