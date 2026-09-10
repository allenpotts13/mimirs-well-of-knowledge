---
type: concept
status: published
created: 2026-09-10
updated: 2026-09-10
technology: SQL
difficulty: Beginner
tags:
  - keys
  - constraints
  - relational-database
aliases:
  - Foreign Key
  - FK
  - Referential Integrity
publish: true
permalink: sql/foreign-keys
---

# <span class="rune">ᛟ</span> Foreign Keys

> A column that references another table's primary key, forming the relationship between two tables.

---

## 🎯 Purpose

A foreign key ties a row in one table to a specific row in another, and stops the database from accepting a value that doesn't correspond to a real row on the other side.

Use it anywhere one table's rows logically belong to a row in another table (an order belongs to a customer, an employee belongs to a department).

---

## 🧠 Key Ideas

- Points to a [[Primary Keys|primary key]] (or unique key) in another table.
- Enforces **referential integrity** — you can't insert a foreign key value that doesn't exist in the referenced table.
- Controls what happens on delete/update of the referenced row via `ON DELETE` / `ON UPDATE` rules (`CASCADE`, `SET NULL`, `RESTRICT`, `NO ACTION`).
- A table can have many foreign keys, each pointing at a different parent table.

---

## ⚙️ How It Works

When a row is inserted or updated, the database checks the foreign key column against the referenced table's primary key. If no matching row exists there, the operation is rejected.

Deleting a referenced row is also checked — by default most databases block the delete unless a cascade rule says otherwise.

---

## 💻 Examples

```sql
CREATE TABLE Orders (
	OrderID INT PRIMARY KEY IDENTITY(1,1),
	CustomerID INT NOT NULL,
	OrderDate DATE,
	FOREIGN KEY (CustomerID) REFERENCES Customers(CustomerID)
		ON DELETE CASCADE
);
```

`ON DELETE CASCADE` here means deleting a customer automatically deletes their orders too — use this rule deliberately, not by default.

---

## 🚀 Real World Applications

- Linking orders to the customer who placed them
- Linking employees to the department they belong to
- Any one-to-many or many-to-many relationship (many-to-many needs a junction table with two foreign keys)

---

## ⚖️ Advantages

- Prevents orphaned or inconsistent data at the database level, not just in application code.
- Makes the schema's relationships self-documenting.
- `ON DELETE`/`ON UPDATE` rules automate cleanup that would otherwise need manual code.

---

## ⚠️ Limitations

- Adds a small overhead to every insert/update/delete that touches the constraint.
- `CASCADE` deletes can silently remove far more data than intended if not fully understood.
- Foreign key columns aren't automatically indexed in every database engine — see [[Missing Indexes]].

---

## 🚨 Common Mistakes

- Not indexing the foreign key column, which makes joins against the parent table slow.
- Using `ON DELETE CASCADE` without realizing how far the cascade actually reaches through the schema.
- Forgetting a foreign key entirely and relying on application code alone to keep data consistent — a bug or a manual script can then create orphaned rows.

---

## 📖 Further Reading

Documentation

Books

Videos

Articles

---

## 💡 Wisdom from Mímir

`ON DELETE CASCADE` is the setting most likely to cause an accident in production. Before adding it, trace exactly what disappears if the parent row is deleted — and if that's ever surprising, use `RESTRICT` or `SET NULL` instead and delete children explicitly.

---

## 🔗 Related Notes

- [[Primary Keys]]
- [[Constraints]]
- [[INNER JOIN]]
- [[Finding Records With a Missing Relationship]]
