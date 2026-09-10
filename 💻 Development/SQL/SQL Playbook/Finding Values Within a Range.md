---
type: query-pattern
status: active
created: 2026-08-13
updated: 2026-08-13
technology: SQL
difficulty: Beginner
tags:
  - between
  - filtering
  - ranges
  - where
aliases:
  - Salary Between Two Values
  - Range Filtering
---

# 🧩 Finding Values Within a Range

> *Use `BETWEEN` to filter values that fall within an inclusive lower and upper boundary.*

---

## 🎯 Problem

Find employees whose salary is between $50,000 and $100,000.

---

## 🤔 Mental Model

---

## 🧠 Why This Pattern Works

`BETWEEN` represents a range:

```text
50000 ≤ salary ≤ 100000
```

The important detail is that **both boundaries are inclusive**.

So these values match:

```text
50000
75000
100000
```

---

## 💻 SQL Solution

```sql
SELECT *
FROM employees
WHERE salary BETWEEN 50000 AND 100000;
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Select the employee records

```sql
SELECT *
FROM employees
```

We're starting with the `employees` table and asking for the employee records we want returned.

---

### Step 2 — Filter the salary column

```sql
WHERE salary BETWEEN 50000 AND 100000;
```

This tells SQL to only return employees whose salary falls within the specified range.

---

### Step 3 — Understand the boundaries

`BETWEEN` is **inclusive**, meaning both endpoints are included.

So this:

```sql
WHERE salary BETWEEN 50000 AND 100000
```

is equivalent to:

```sql
WHERE salary >= 50000
  AND salary <= 100000;
```

Therefore, all of these would match:

50000
75000
100000

But these would not:

49999
100001

---

### Step 4 — Put the pattern together

```sql
SELECT *
FROM employees
WHERE salary BETWEEN 50000 AND 100000;

```

The thought process is:

Start with employees

        ↓

Look at salary

        ↓

Keep values >= 50,000

        ↓

Keep values <= 100,000

        ↓

Return matching employees

---

## 🚀 Common Use Cases

- Salary ranges
- Age ranges
- Price ranges
- Numeric thresholds
- Date ranges
- Score ranges

---

## ⚖️ Alternatives

The same logic can be written as:

```sql
SELECT *
FROM employees
WHERE salary >= 50000
  AND salary <= 100000;
```

This is useful to remember because it makes the inclusive boundaries explicit.

---

## 🧪 Excluding the Boundaries

If the requirement is strictly greater than $50,000 and strictly less than $100,000:

```sql
WHERE salary > 50000
  AND salary < 100000;
```

Don't use `BETWEEN` when the endpoints should be excluded.


---

## ⚠️ Performance Considerations

- ange predicates can make good use of indexes on the filtered column.
- Avoid wrapping an indexed column in a function when a direct range comparison can be used.
- The database may choose a table scan when a large percentage of rows match.
- For date/time filtering, half-open ranges are often preferable:

```sql
>= start
< next_period
```

---

## 🚨 Common Mistakes

- Forgetting that `BETWEEN` is inclusive.
- Reversing the lower and upper values.
- Using `BETWEEN` when exclusive boundaries are required.
- Using `BETWEEN` carelessly with timestamps.


## ⚠️ Important Date Consideration

`BETWEEN` can be tricky with timestamps.

For example:

```sql
WHERE created_at BETWEEN '2026-01-01' AND '2026-12-31'
```

may not include all timestamps occurring on December 31, depending on the database and data type.

A safer timestamp pattern is often:

```sql
WHERE created_at >= '2026-01-01'
  AND created_at < '2027-01-01'
```

---

## 💡 Wisdom from Mímir

Whenever you see:

> **"between X and Y"**

translate it mentally into:

```text
>= X
AND
<= Y
```

Then ask:

> **Should the boundaries actually be included?**

---

## 🔗 Related Notes

- [[Filtering Values That Are a Multiple of a Number]] — another numeric WHERE-clause filter, testing divisibility instead of a range
- [[Bucketing Values Into Ranges With CASE]] — turns this same kind of range check into several labeled buckets in one query
- [[Finding Values in a Statistical Percentile]] — a range defined statistically (relative to the data's distribution) rather than by fixed literal bounds