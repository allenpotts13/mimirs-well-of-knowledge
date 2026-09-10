---
type: concept
status: published
created: 2026-09-10
updated: 2026-09-10
technology: SQL
difficulty: Intermediate
tags:
  - transactions
  - acid
  - data-integrity
aliases:
  - ACID
  - BEGIN COMMIT ROLLBACK
publish: true
permalink: sql/transactions
---

# <span class="rune">ᛟ</span> Transactions

> Groups multiple statements into a single all-or-nothing unit of work.

---

## 🎯 Purpose

Transactions guarantee that a set of related changes either all succeed together or all fail together — the database is never left half-updated.

Use them whenever two or more statements must stay in sync with each other.

---

## 🧠 Key Ideas

- Controlled with `BEGIN` (or `START TRANSACTION`), `COMMIT`, and `ROLLBACK`.
- Guided by the **ACID** properties:
  - **Atomicity** — all statements succeed, or none do
  - **Consistency** — the database moves from one valid state to another
  - **Isolation** — concurrent transactions don't see each other's uncommitted changes
  - **Durability** — once committed, changes survive a crash
- Nothing is permanent until `COMMIT` runs.
- `ROLLBACK` undoes every statement in the transaction back to its starting state.

---

## ⚙️ How It Works

Statements inside a transaction execute normally, but their effects stay provisional until the transaction ends.

```text
BEGIN
  UPDATE Accounts SET Balance = Balance - 100 WHERE AccountID = 1;  -- debit
  UPDATE Accounts SET Balance = Balance + 100 WHERE AccountID = 2;  -- credit
COMMIT  -- both changes become permanent together
```

If the second statement fails, a `ROLLBACK` reverses the first one too — the money is never lost mid-transfer.

---

## 💻 Examples

```sql
BEGIN TRANSACTION;

UPDATE Inventory SET Quantity = Quantity - 1 WHERE ProductID = 42;
INSERT INTO Orders (ProductID, CustomerID) VALUES (42, 7);

COMMIT;
```

```sql
BEGIN TRANSACTION;

UPDATE Accounts SET Balance = Balance - 100 WHERE AccountID = 1;
-- something goes wrong here
ROLLBACK;  -- the debit above never happened, as far as the database is concerned
```

---

## 🚀 Real World Applications

- Bank transfers (debit one account, credit another)
- Order processing (decrement inventory + create the order record)
- Any multi-table update that must stay logically consistent

---

## ⚖️ Advantages

- Guarantees data consistency even when something fails mid-operation.
- Removes the need for manual "undo" logic in application code.
- Isolation prevents other users from seeing half-finished work.

---

## ⚠️ Limitations

- Long-running transactions hold locks and hurt concurrency for other users — see [[Deadlocks]].
- Not a substitute for proper error handling in application code.
- Isolation levels trade consistency guarantees against performance — stricter isolation means more locking.

---

## 🚨 Common Mistakes

- Forgetting to `COMMIT` — changes sit uncommitted and invisible to other sessions until the connection closes.
- Wrapping unrelated work into one giant transaction, holding locks far longer than necessary.
- Not handling the case where a transaction fails partway and needs an explicit `ROLLBACK`.
- Assuming transactions protect against application-level bugs — they only protect against partial database writes.

---

## 📖 Further Reading

Documentation

Books

Videos

Articles

---

## 💡 Wisdom from Mímir

Keep transactions as short as possible. The longer one holds locks, the more likely it collides with another transaction — see [[Deadlocks]]. If a transaction is doing anything slow (calling an external API, waiting on user input), that work belongs outside the transaction boundary.

---

## 🔗 Related Notes

- [[Deadlocks]]
- [[INNER JOIN]]
- [[Constraints]]
