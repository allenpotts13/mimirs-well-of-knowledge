---
type: concept
status: published
created: 2026-09-10
updated: 2026-09-10
technology: SQL
difficulty: Intermediate
tags:
  - stored-procedures
  - database-objects
aliases:
  - SPROC
  - Stored Procedure
publish: true
---

# 📚 Stored Procedures

> A named, precompiled block of SQL saved in the database and executed by calling its name — logic that lives with the data instead of in application code.

---

## 🎯 Purpose

A stored procedure packages one or more SQL statements — including logic, parameters, and control flow — into a single callable unit stored inside the database itself.

Use it to centralize logic that multiple applications need to share, or to reduce round-trips between the application and the database.

---

## 🧠 Key Ideas

- Accepts input parameters and can return output values or result sets.
- Can contain procedural logic (`IF`, loops, variables) beyond what a single query can express, unlike a [[Views|view]].
- Runs inside the database engine — no need to send multiple separate queries across the network.
- Often used together with [[Transactions]] to guarantee multi-step logic is all-or-nothing.

---

## ⚙️ How It Works

```sql
CREATE PROCEDURE GetCustomerOrders
	@CustomerID INT
AS
BEGIN
	SELECT OrderID, OrderDate, TotalAmount
	FROM Orders
	WHERE CustomerID = @CustomerID
	ORDER BY OrderDate DESC;
END;
```

Calling it is a single statement, regardless of how much logic runs underneath:

```sql
EXEC GetCustomerOrders @CustomerID = 7;
```

---

## 💻 Examples

```sql
CREATE PROCEDURE TransferFunds
	@FromAccount INT,
	@ToAccount INT,
	@Amount DECIMAL(10,2)
AS
BEGIN
	BEGIN TRANSACTION;

	UPDATE Accounts SET Balance = Balance - @Amount WHERE AccountID = @FromAccount;
	UPDATE Accounts SET Balance = Balance + @Amount WHERE AccountID = @ToAccount;

	COMMIT;
END;
```

---

## 🚀 Real World Applications

- Encapsulating a multi-step business operation (like a funds transfer) behind one call
- Sharing the same validated logic across multiple applications hitting the same database
- Batch or scheduled maintenance operations

---

## ⚖️ Advantages

- Reduces network round-trips — one call instead of several separate statements.
- Centralizes logic so every caller behaves consistently, instead of re-implementing it per application.
- Precompiled execution plans can improve performance for frequently-run logic.

---

## ⚠️ Limitations

- Business logic split between application code and stored procedures can become hard to track and version.
- Harder to unit test than application-layer code in most tooling.
- Ties logic to a specific database engine's procedural SQL dialect, hurting portability.

---

## 🚨 Common Mistakes

- Putting so much business logic into procedures that the application layer becomes just a thin caller, making the system harder to reason about as a whole.
- Not wrapping multi-step procedures in a [[Transactions|transaction]], leaving partial changes possible on failure.
- Losing track of which procedures exist and what depends on them, since they live outside the application's normal codebase/version control unless deliberately tracked.

---

## 📖 Further Reading

Documentation

Books

Videos

Articles

---

## 💡 Wisdom from Mímir

Version-control stored procedures the same way as application code — a `.sql` file per procedure in the repo, deployed through migrations. Logic living only inside the database, invisible to `git log`, is one of the easiest ways for a team to lose track of what a system actually does.

---

## 🔗 Related Notes

- [[Transactions]]
- [[Views]]
