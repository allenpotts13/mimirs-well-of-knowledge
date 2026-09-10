---
type: query-pattern
status: published
created: 2026-09-02
updated: 2026-09-02
technology: SQL
difficulty: Advanced
tags:
  - percentiles
  - window-functions
  - aggregate-functions
  - database-portability
aliases:
  - Top 10 Percent Salaries
  - PERCENTILE_CONT Alternative
  - Finding Percentile Cutoffs
publish: true
permalink: sql/finding-values-in-a-statistical-percentile
---

# <span class="rune">ᚲ</span> Finding Values in a Statistical Percentile

> *Use a percentile calculation to find the cutoff value that separates the top (or bottom) fraction of a dataset, then filter against it.*

---

## 🎯 Problem

Find employees whose salary is in the top 10%.

More generally, this pattern answers:
> What is the cutoff value above which only the top X% of rows fall — and which rows exceed it?

---

## 🤔 Mental Model

Picture lining up every employee shortest-to-tallest by salary, then walking 90% of the way down the line. Whoever is standing at that point marks the boundary: everyone behind them (the remaining 10%) is the "top 10%." A percentile calculation is just an automated way of finding where in that line the 90% mark falls.

---

## 🧠 Why This Pattern Works

A percentile answers "what value has X% of the data at or below it?" `PERCENTILE_CONT(0.9)` conceptually sorts every salary, then interpolates the value sitting at the 90th percentile position in that sorted list. Filtering `WHERE salary >= <that value>` then keeps only the rows at or beyond that boundary — the top 10% by definition.

> **⚠️ Portability note:** `PERCENTILE_CONT` is **not available in MySQL.** It exists in PostgreSQL, Oracle, and SQL Server, but MySQL (including MySQL 8.0) has no built-in percentile function. The query below is the conceptually correct, standard-SQL way to express "top 10%," but it will fail with a syntax error if run directly against MySQL. See the Alternatives section for the MySQL-compatible way to get the same result using `PERCENT_RANK()`.

---

## 💻 SQL Solution

```sql
-- Standard SQL / PostgreSQL / Oracle / SQL Server — NOT valid in MySQL
SELECT * FROM employees
WHERE salary >= (
  SELECT PERCENTILE_CONT(0.9) WITHIN GROUP (ORDER BY salary ASC)
  FROM employees
);
```

`WITHIN GROUP (ORDER BY salary ASC)` tells `PERCENTILE_CONT` which ordering to interpolate across — it must always be paired with an explicit `ORDER BY`.

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Sort the values conceptually

```sql
ORDER BY salary ASC
```

`PERCENTILE_CONT` treats the data as if sorted ascending, from lowest to highest salary.

### Step 2 — Interpolate the 90th percentile value

```sql
PERCENTILE_CONT(0.9) WITHIN GROUP (ORDER BY salary ASC)
```

Finds (interpolating between actual data points if necessary) the salary value at the 90% mark of the sorted list.

### Step 3 — Use that value as a cutoff

```sql
WHERE salary >= (...)
```

The outer query keeps every employee whose salary meets or exceeds that cutoff — the top 10% of earners.

---

## 🧪 Example Data

| employee | salary |
| -------- | -----: |
| Alice    |  40000 |
| Bob      |  45000 |
| Carol    |  50000 |
| Dave     |  60000 |
| Eve      |  95000 |
| Frank    | 120000 |
| Grace    | 130000 |
| Henry    | 140000 |
| Ivy      | 150000 |
| Jack     | 200000 |

With 10 rows, the 90th percentile falls at the value below which 90% of salaries sit — here, roughly Jack's 200000 (the single highest earner is the top 10%).

Query (standard SQL):
```sql
SELECT * FROM employees
WHERE salary >= (
  SELECT PERCENTILE_CONT(0.9) WITHIN GROUP (ORDER BY salary ASC)
  FROM employees
);
```

Result: Jack (200000).

---

## 🚀 Common Use Cases

- Identifying top-earning employees for compensation review
- Flagging the slowest-loading pages (95th/99th percentile response time)
- Finding high-value customers by spend (top 10% of order totals)
- Setting SLA thresholds based on historical percentile latency
- Detecting outlier transactions for fraud review
- Grading on a curve (top/bottom percentile of test scores)

---

## ⚖️ Alternatives

### MySQL-compatible: PERCENT_RANK() window function

```sql
SELECT * FROM (
  SELECT *, PERCENT_RANK() OVER (ORDER BY salary ASC) AS pct_rank
  FROM employees
) AS ranked
WHERE pct_rank >= 0.9;
```

`PERCENT_RANK()` (available in MySQL 8.0+) assigns each row a relative rank between 0 and 1 based on its position in the sorted order. Filtering `pct_rank >= 0.9` keeps roughly the top 10% of rows. Note this is rank-based rather than interpolation-based, so its exact cutoff can differ slightly from `PERCENTILE_CONT` on small datasets — with `PERCENT_RANK`, the lowest-ranked row always gets exactly 0 and the highest exactly 1, so results are sensitive to dataset size.

### MySQL-compatible: manual OFFSET/LIMIT on sorted data

```sql
SELECT *
FROM employees
ORDER BY salary DESC
LIMIT CEIL((SELECT COUNT(*) FROM employees) * 0.1);
```

Sorts salaries descending and takes however many rows make up the top 10% by row count rather than by value. This answers a subtly different question ("top 10% of *rows*" vs. "rows above the 90th-percentile *value*") but is often what people actually want, and it's simple and portable.

### Variables-based percentile (older MySQL, pre-8.0)

```sql
SET @row_count = (SELECT COUNT(*) FROM employees);
SELECT * FROM (
  SELECT e.*, @rownum := @rownum + 1 AS rn
  FROM employees e, (SELECT @rownum := 0) r
  ORDER BY salary ASC
) ranked
WHERE rn > @row_count * 0.9;
```

A pre-window-function workaround using session variables to simulate row numbering — mostly of historical interest now that `PERCENT_RANK()` and `NTILE()` are available in MySQL 8.0+.

---

## ⚠️ Performance Considerations

- `PERCENTILE_CONT` and `PERCENT_RANK()` both effectively require sorting the full dataset (or the relevant partition), which can be expensive on very large tables.
- **Again: `PERCENTILE_CONT` does not exist in MySQL at all** — attempting to run it there fails immediately, regardless of table size or indexing. Confirm your target database supports it before reaching for it.
- An index on the column being ranked (`salary`) can help the database avoid a full sort-from-scratch, though window functions like `PERCENT_RANK()` still typically require examining the whole ordered set.
- For repeated percentile queries on a large, slowly-changing table, consider precomputing and caching percentile cutoffs rather than recalculating them on every query.

---

## 🚨 Common Mistakes

- Running `PERCENTILE_CONT` against MySQL and assuming the resulting syntax error means the query is malformed, rather than realizing the function simply doesn't exist there.
- Confusing "top 10% of values" (percentile-based, using `PERCENTILE_CONT` or `PERCENT_RANK`) with "top 10% of rows" (count-based, using `LIMIT`) — these can return different results, especially with duplicate or skewed values.
- Forgetting `WITHIN GROUP (ORDER BY ...)` when using `PERCENTILE_CONT`, which is required syntax, not optional.
- Using `PERCENTILE_DISC` when `PERCENTILE_CONT` (or vice versa) was intended — `_DISC` returns an actual value from the dataset, while `_CONT` interpolates between values, and they can disagree.
- Assuming percentile cutoffs are stable — on a frequently-changing table, "the top 10%" is a moving target that must be recalculated, not a fixed value to hardcode.

---

## 💡 Wisdom from Mímir

Not every dialect of SQL speaks every sentence. `PERCENTILE_CONT` is fluent, elegant standard SQL — and MySQL simply does not understand it.

> Before asking "how do I write this query," ask: **"does my database even have the words for it?"**

Knowing a pattern's ideal syntax is only half the skill; knowing which engines refuse to speak it — and having a fallback ready — is the other half.

---

## 🔗 Related Notes

- [[Finding Values Above the Average]]
- [[Retrieving the Top N Highest Values]]
- [[Finding the Nth Highest Value]]
- [[Ranking the Top N Rows Within Each Group]]
