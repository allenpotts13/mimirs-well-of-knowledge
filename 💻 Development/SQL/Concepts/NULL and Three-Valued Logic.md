---
type: concept
status: active
created: 2026-09-10
updated: 2026-09-10
technology: SQL
difficulty: Intermediate
tags:
  - null
  - fundamentals
aliases:
  - Three-Valued Logic
  - IS NULL
---

# 📚 NULL and Three-Valued Logic

> NULL means "unknown," not "empty" or "zero" — and that single distinction breaks a lot of intuitive-looking SQL.

---

## 🎯 Purpose

NULL represents the *absence* of a known value. SQL logic isn't just true/false — it's true, false, or **unknown**, and unknown propagates through comparisons in ways that trip up nearly everyone at some point.

---

## 🧠 Key Ideas

- `NULL` is not the same as `0`, an empty string `''`, or `false` — it means "no value is recorded here."
- Any direct comparison against `NULL` (`= NULL`, `<> NULL`) evaluates to **unknown**, not true or false — use `IS NULL` / `IS NOT NULL` instead.
- `unknown` behaves like `false` for filtering purposes — a row where the condition evaluates to unknown is excluded from `WHERE` results.
- Aggregate functions (`COUNT`, `SUM`, `AVG`) generally ignore NULLs — except `COUNT(*)`, which counts rows regardless of NULLs.
- `NULL` values sort first or last depending on the database engine — never assume which without checking.

---

## ⚙️ How It Works

```text
5 = 5        → true
5 = 3        → false
5 = NULL     → unknown   (not false!)
NULL = NULL  → unknown   (NULL isn't even equal to itself)
```

Because `5 = NULL` evaluates to unknown rather than false, `WHERE Column = NULL` never matches any row — even rows that are genuinely NULL. That's exactly why `IS NULL` exists as separate syntax.

---

## 💻 Examples

```sql
-- Wrong: this returns zero rows, always
SELECT * FROM Customers WHERE Email = NULL;

-- Correct
SELECT * FROM Customers WHERE Email IS NULL;

-- COUNT behavior
SELECT
	COUNT(*) AS TotalRows,        -- counts every row
	COUNT(Email) AS RowsWithEmail -- ignores NULL emails
FROM Customers;
```

---

## 🚀 Real World Applications

- Finding rows with missing/incomplete data (`IS NULL`)
- Handling optional foreign keys correctly (see [[LEFT JOIN]])
- Writing `CASE` expressions and aggregates that need to account for missing values with `COALESCE`/`ISNULL`

---

## ⚖️ Advantages

- Lets a column represent "no value recorded" distinctly from a real zero or empty string.
- Three-valued logic is consistent once understood — it just isn't the two-valued logic most languages use.

---

## ⚠️ Limitations

- Every comparison, join, and aggregate needs to be reasoned about with NULL in mind — it's easy to forget a case.
- `NOT IN` with a list that contains a NULL silently returns zero rows for the entire query, a notoriously easy trap to fall into.

---

## 🚨 Common Mistakes

- Writing `WHERE Column = NULL` instead of `WHERE Column IS NULL` — this silently matches nothing, with no error.
- Using `NOT IN (SELECT ...)` against a subquery that can return NULL — a single NULL in that list makes the entire `NOT IN` match nothing.
- Assuming `COUNT(Column)` and `COUNT(*)` are interchangeable — they're not, once NULLs are involved.
- Forgetting `AVG()`/`SUM()` skip NULLs rather than treating them as zero, which changes the calculated average.

---

## 📖 Further Reading

Documentation

Books

Videos

Articles

---

## 💡 Wisdom from Mímir

Treat `NOT IN` against any subquery as a small landmine — one unexpected NULL in that list silently empties the whole result. `NOT EXISTS` doesn't have this problem and is usually the safer default for "not in this set" logic.

---

## 🔗 Related Notes

- [[LEFT JOIN]]
- [[Filtering Rows With a NULL Column]]
- [[Subqueries]]
