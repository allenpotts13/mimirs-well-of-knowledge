---
type: query-pattern
status: active
created: 2026-08-13
updated: 2026-08-13
technology: SQL
difficulty: Beginner
tags:
  - order-by
  - limit
  - dates
  - sorting
aliases:
  - Youngest Employee
  - Most Recent Birth Date
---

# 🧩 Finding the Youngest Employee

> *Sort birth dates from most recent to oldest and return the first record.*

---

## 🎯 Problem

Find the youngest employee in the organization.

Assume the employee's date of birth is stored in:

```text
birth_date
```

---

## 🤔 Mental Model

This is a classic example of reasoning about dates.

A younger person has a **more recent birth date**.

For example:

```text
1975-04-12
1985-09-21
1998-02-10  ← youngest
```

Therefore:

```sql
ORDER BY birth_date DESC
```

puts the youngest person first.


---

## 🧠 Why This Pattern Works

### Youngest

```sql
ORDER BY birth_date DESC
LIMIT 1;
```

### Oldest

```sql
ORDER BY birth_date ASC
LIMIT 1;
```

Remember:

```text
Youngest → newest birth date → DESC
Oldest   → oldest birth date  → ASC
```

---

## 💻 SQL Solution

```sql
SELECT *
FROM employees
ORDER BY birth_date DESC
LIMIT 1;
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Select the employee records

```sql
SELECT *
FROM employees
```

We're starting with the employee table and asking for the employee information we want returned.

---

### Step 2 — Sort by birth date

```sql
ORDER BY birth_date DESC
```

This is the key part of the query.

`DESC` sorts dates from **most recent → oldest**.

For example:

|Employee|Birth Date|
|---|---|
|Alice|1975-04-12|
|Bob|1988-06-21|
|Carol|1998-02-10|
|David|2001-11-05|

After:

```sql
ORDER BY birth_date DESC
```

the order becomes:

|Employee|Birth Date|
|---|---|
|David|2001-11-05|
|Carol|1998-02-10|
|Bob|1988-06-21|
|Alice|1975-04-12|

The most recently occurring birth date is therefore at the top.

---

### Step 3 — Return only the first record

```sql
LIMIT 1;
```

`LIMIT 1` tells the database:

> Only return the first row after sorting.

Since the youngest employee is now first, that gives us the youngest employee.

---

### Step 4 — Put the pattern together

```sql
SELECT *
FROM employees
ORDER BY birth_date DESC
LIMIT 1;
```

The entire thought process is:

Find employees

      ↓

Sort by birth date

      ↓

Newest birth date first

      ↓

Take the first row

      ↓

Youngest employee

---

## 🚀 Common Use Cases

- Youngest employee
- Oldest employee
- Most recent customer
- Latest transaction
- Most recent login
- Earliest event
- First or last record by date

---

## ⚖️ Alternatives

SQL Server uses `TOP` instead of `LIMIT`:

```sql
SELECT TOP 1 *
FROM employees
ORDER BY birth_date DESC;
```

---

## 🧪 Handling Ties

If two employees share the exact same birth date, `LIMIT 1` returns only one of them.

If you need every employee sharing the youngest birth date:

```sql
SELECT *
FROM employees
WHERE birth_date = (
    SELECT MAX(birth_date)
    FROM employees
);
```

Because the most recent birth date represents the youngest employee.

---

## ⚠️ Performance Considerations

- An index on `birth_date` can make finding the highest/lowest date very efficient.
- `ORDER BY ... LIMIT 1` may allow the database to stop after finding the first qualifying row when an appropriate index exists.
- `MAX(birth_date)` can also be highly efficient with an index.
- If ties matter, don't blindly use `LIMIT 1`.

---

## 🚨 Common Mistakes

- Using `ASC` when looking for the youngest.
- Forgetting `LIMIT 1`.
- Assuming `LIMIT` works in every SQL dialect.
- Ignoring ties.
- Confusing the youngest person with the person who was hired most recently.


---

## 💡 Wisdom from Mímir

When working with dates, don't just memorize `ASC` and `DESC`.

Ask:

> **What does "higher" or "lower" mean for this date?**

For birth dates:

```text
More recent birth date = Younger
Earlier birth date     = Older
```

For hire dates:

```text
More recent hire date = Hired later
Earlier hire date     = Hired earlier
```

The same SQL sorting mechanism can answer completely different business questions.


---

## 🔗 Related Notes

- [[Calculating Age from a Birth Date]] — computes an actual age from the same birth_date column instead of just sorting by it
- [[How to Fetch the First and Last Record from a Table]] — the same ORDER BY + LIMIT 1 shape, generalized to grab both ends at once
- [[Filtering Records by a Recent Date Window]] — another "how should DESC/ASC map to the business meaning of a date" pattern