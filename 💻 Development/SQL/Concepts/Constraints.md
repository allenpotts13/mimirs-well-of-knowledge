---
type: concept
status: active
created: 2026-09-10
updated: 2026-09-10
technology: SQL
difficulty: Beginner
tags:
  - constraints
  - data-integrity
aliases:
  - NOT NULL
  - UNIQUE Constraint
  - CHECK Constraint
---

# 📚 Constraints

> Rules attached to a column or table that the database enforces on every insert and update, no exceptions.

---

## 🎯 Purpose

Constraints push data-integrity rules down into the database itself, so bad data can't get in even if application code has a bug.

Use them for any rule that must always be true, regardless of which application or script is writing to the table.

---

## 🧠 Key Ideas

- **NOT NULL** — the column must always have a value.
- **UNIQUE** — no two rows can share the same value in this column.
- **CHECK** — a custom condition every row must satisfy (e.g. `Price >= 0`).
- **DEFAULT** — a fallback value used when no value is provided on insert.
- **PRIMARY KEY** and **FOREIGN KEY** are also constraints — see [[Primary Keys]] and [[Foreign Keys]].

---

## ⚙️ How It Works

Every `INSERT` or `UPDATE` is checked against all constraints on the table before the change is applied. If any constraint fails, the entire statement is rejected — nothing is partially written.

---

## 💻 Examples

```sql
CREATE TABLE Products (
	ProductID INT PRIMARY KEY IDENTITY(1,1),
	SKU VARCHAR(20) NOT NULL UNIQUE,
	Price DECIMAL(10,2) NOT NULL CHECK (Price >= 0),
	Status VARCHAR(20) DEFAULT 'Active'
);
```

Here, a `SKU` must exist and be unique, `Price` can never be negative, and `Status` quietly defaults to `'Active'` if not specified.

---

## 🚀 Real World Applications

- Preventing negative prices, quantities, or ages
- Guaranteeing an email or username column has no duplicates
- Making sure required fields (like a customer's name) are never left blank
- Giving new rows a sensible default without every INSERT needing to specify it

---

## ⚖️ Advantages

- Enforced at the database level — no application, script, or manual query can bypass it.
- Catches data problems immediately, at write time, instead of during a report months later.
- Self-documents the rules of the schema for anyone reading the table definition.

---

## ⚠️ Limitations

- A missing or overly strict constraint can block legitimate data (e.g. a CHECK that doesn't account for a valid edge case).
- Adding a constraint to a table that already has violating data will fail until that data is cleaned up.

---

## 🚨 Common Mistakes

- Relying only on application-level validation and skipping database constraints — a second application, a migration script, or a manual fix can bypass app-level checks entirely.
- Forgetting `NOT NULL` on a column that should always be required.
- Writing a `CHECK` constraint that's technically correct but doesn't match a real business rule someone later needs to work around.

---

## 📖 Further Reading

Documentation

Books

Videos

Articles

---

## 💡 Wisdom from Mímir

Application-level validation is a UX feature — it gives a fast, friendly error message. Database constraints are the actual safety net. Keep both, but never trust the app-level one alone to guarantee data integrity.

---

## 🔗 Related Notes

- [[Primary Keys]]
- [[Foreign Keys]]
- [[Transactions]]
