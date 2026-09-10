---
type: concept
status: active
created: 2026-09-10
updated: 2026-09-10
technology: SQL
difficulty: Beginner
tags:
  - joins
  - relational-database
aliases:
  - SQL RIGHT JOIN
  - RIGHT OUTER JOIN
---

# 📚 RIGHT JOIN

> Returns every row from the right table, plus matching rows from the left table — the mirror image of LEFT JOIN.

---

## 🎯 Purpose

RIGHT JOIN preserves every row from the second (right) table, even when there's no matching row in the first (left) table.

It exists for completeness, but in practice most people rewrite it as a LEFT JOIN with the table order swapped, since that reads more naturally left-to-right.

---

## 🧠 Key Ideas

- Keeps every row from the right table; NULLs fill in unmatched left-table columns.
- Functionally identical to a LEFT JOIN with the two tables swapped.
- Rare in real codebases — mostly seen when adapting someone else's query rather than writing one from scratch.

---

## ⚙️ How It Works

Same matching logic as LEFT JOIN, just applied to the opposite table.

```text
A RIGHT JOIN B  ==  B LEFT JOIN A
```

---

## 💻 Examples

```sql
SELECT
	c.CustomerName,
	o.OrderDate
FROM Orders o
RIGHT JOIN Customers c
	ON c.CustomerID = o.CustomerID;
```

This returns the exact same result as:

```sql
SELECT
	c.CustomerName,
	o.OrderDate
FROM Customers c
LEFT JOIN Orders o
	ON c.CustomerID = o.CustomerID;
```

---

## 🚀 Real World Applications

- Maintaining an existing query that was already written with RIGHT JOIN
- Situations where the "primary" table naturally sits on the right side of a multi-join chain

---

## ⚖️ Advantages

- No functional advantage over LEFT JOIN — purely a readability/style choice.

---

## ⚠️ Limitations

- Reads less naturally than LEFT JOIN for most people, since English sentences tend to describe "the thing we care about" first.
- Team style guides often ban it in favor of LEFT JOIN, purely for consistency.

---

## 🚨 Common Mistakes

- Using RIGHT JOIN out of habit when a LEFT JOIN with reordered tables would be clearer.
- Losing track of which table is "preserved" once a query has several RIGHT and LEFT JOINs mixed together.

---

## 📖 Further Reading

Documentation

Books

Videos

Articles

---

## 💡 Wisdom from Mímir

I've never had a reason to write a fresh RIGHT JOIN — every one I've written was rewritten as a LEFT JOIN within the week for readability. Worth knowing it exists, not worth reaching for.

---

## 🔗 Related Notes

- [[LEFT JOIN]]
- [[INNER JOIN]]
