---
publish: true
status: published
---

# <span class="rune">ᛊ</span> SQL Cheat Sheet

> Dense reference across standard SQL. Each section links back to the full [[SQL Codex|concept note]] for depth.

---

## Query Execution Order

Written top to bottom, but *executed* in this order — see [[Query Execution Order]]:

```text
FROM → JOIN → WHERE → GROUP BY → HAVING → SELECT → DISTINCT → ORDER BY → LIMIT/TOP
```

---

## SELECT Basics

```sql
SELECT DISTINCT column1, column2
FROM table_name
WHERE condition
ORDER BY column1 ASC, column2 DESC
LIMIT 10;          -- TOP 10 in SQL Server, ROWNUM in Oracle
```

| Clause | Purpose |
|---|---|
| `DISTINCT` | remove duplicate rows from the result |
| `ORDER BY` | sort results; `ASC` default, `DESC` for reverse |
| `LIMIT` / `TOP` / `FETCH` | cap the number of returned rows |
| `AS` | alias a column or table |

---

## Filtering — WHERE Operators

| Operator | Meaning |
|---|---|
| `=`, `<>` / `!=`, `<`, `>`, `<=`, `>=` | standard comparisons |
| `AND`, `OR`, `NOT` | combine conditions |
| `BETWEEN a AND b` | inclusive range |
| `IN (a, b, c)` | matches any value in the list |
| `LIKE 'pattern%'` | pattern match — `%` = any chars, `_` = one char |
| `IS NULL` / `IS NOT NULL` | never use `= NULL` — see [[NULL and Three-Valued Logic]] |
| `EXISTS (subquery)` | true if the subquery returns any row |

```sql
WHERE Price BETWEEN 10 AND 50
WHERE Email LIKE '%@gmail.com'
WHERE Status IN ('Active', 'Pending')
WHERE DeletedAt IS NULL
```

See: [[Finding Values Within a Range]], [[Matching Text Patterns with LIKE]], [[Filtering Rows With a NULL Column]]

---

## Joins

| Join | Returns |
|---|---|
| `INNER JOIN` | only rows matching in both tables |
| `LEFT JOIN` | all of left table + matches from right (NULL if none) |
| `RIGHT JOIN` | all of right table + matches from left (NULL if none) |
| `FULL OUTER JOIN` | all rows from both tables, matched where possible |
| `CROSS JOIN` | every row × every row (Cartesian product) |
| Self-join | a table joined to itself, usually for hierarchical data |

```sql
SELECT c.CustomerName, o.OrderDate
FROM Customers c
LEFT JOIN Orders o ON c.CustomerID = o.CustomerID;
```

See: [[INNER JOIN]] · [[LEFT JOIN]] · [[RIGHT JOIN]] · [[FULL OUTER JOIN]] · [[CROSS JOIN]] · [[Comparing Related Rows With a Self-Join]]

---

## Aggregation

| Function | Purpose |
|---|---|
| `COUNT(*)` / `COUNT(col)` | row count / non-NULL value count |
| `SUM(col)` | total |
| `AVG(col)` | mean (ignores NULLs) |
| `MIN(col)` / `MAX(col)` | smallest / largest value |

```sql
SELECT Department, COUNT(*) AS Headcount, AVG(Salary) AS AvgSalary
FROM Employees
WHERE HireDate >= '2020-01-01'   -- filters rows
GROUP BY Department
HAVING COUNT(*) > 5;              -- filters groups
```

**WHERE filters rows before grouping. HAVING filters groups after aggregation — never put an aggregate condition in WHERE.**

See: [[GROUP BY and HAVING]] · [[Aggregate Functions]]

---

## Window Functions

```sql
function_name() OVER (
	PARTITION BY column   -- optional: defines the group
	ORDER BY column        -- required for ranking functions
)
```

| Function | Behavior on ties |
|---|---|
| `ROW_NUMBER()` | always unique, even for ties |
| `RANK()` | ties share a rank, next rank skips (1,1,3) |
| `DENSE_RANK()` | ties share a rank, no gap (1,1,2) |
| `LAG(col)` / `LEAD(col)` | value from the previous / next row |
| `SUM()`/`AVG() OVER(...)` | running total / moving average |

```sql
SELECT employee_name, salary,
	DENSE_RANK() OVER (ORDER BY salary DESC) AS salary_rank
FROM employees;
```

See: [[Window Functions]] · [[Finding the Nth Highest Value]] · [[Ranking the Top N Rows Within Each Group]]

---

## Subqueries

```sql
-- Non-correlated (runs once)
SELECT * FROM Products WHERE Price > (SELECT AVG(Price) FROM Products);

-- Correlated (runs once per outer row)
SELECT * FROM Employees e
WHERE Salary > (SELECT AVG(Salary) FROM Employees e2 WHERE e2.Department = e.Department);
```

See: [[Subqueries]] · [[Common Table Expressions]]

---

## Set Operations

| Operator | Behavior |
|---|---|
| `UNION` | combine + remove duplicates |
| `UNION ALL` | combine, keep duplicates (faster — default choice) |
| `INTERSECT` | rows present in both queries |
| `EXCEPT` / `MINUS` | rows in the first query, not the second |

See: [[UNION vs UNION ALL]]

---

## Data Modification (DML)

```sql
INSERT INTO table_name (col1, col2) VALUES (val1, val2);

UPDATE table_name SET col1 = val1 WHERE condition;

DELETE FROM table_name WHERE condition;

MERGE INTO target USING source ON target.id = source.id
	WHEN MATCHED THEN UPDATE SET target.col = source.col
	WHEN NOT MATCHED THEN INSERT (col) VALUES (source.col);
```

> Always run the `SELECT` version of the `WHERE` clause first before an `UPDATE`/`DELETE` — there's no undo once it's committed.

See: [[UPDATE with JOIN]] · [[MERGE Statement]] · [[Updating Values Conditionally with CASE]]

---

## Data Definition (DDL)

```sql
CREATE TABLE table_name (
	id INT PRIMARY KEY IDENTITY(1,1),
	name VARCHAR(100) NOT NULL,
	price DECIMAL(10,2) CHECK (price >= 0),
	created_at DATETIME DEFAULT GETDATE()
);

ALTER TABLE table_name ADD column_name VARCHAR(50);
ALTER TABLE table_name DROP COLUMN column_name;

DROP TABLE table_name;
TRUNCATE TABLE table_name;   -- resets the table, minimal logging, usually no rollback
```

See: [[DDL vs DML vs DCL vs TCL]] · [[Constraints]] · [[Emptying a Table with TRUNCATE]]

---

## Keys & Constraints

```sql
PRIMARY KEY                          -- unique, never NULL, one per table
FOREIGN KEY REFERENCES other(id)     -- must match a row in the referenced table
	ON DELETE CASCADE | SET NULL | RESTRICT
UNIQUE                                -- no duplicate values
NOT NULL                              -- value required
CHECK (condition)                     -- custom rule
DEFAULT value                         -- fallback when no value given
```

See: [[Primary Keys]] · [[Foreign Keys]] · [[Constraints]]

---

## Transactions

```sql
BEGIN TRANSACTION;

UPDATE Accounts SET Balance = Balance - 100 WHERE AccountID = 1;
UPDATE Accounts SET Balance = Balance + 100 WHERE AccountID = 2;

COMMIT;      -- makes both changes permanent
-- or
ROLLBACK;    -- undoes everything since BEGIN
```

**ACID**: Atomicity · Consistency · Isolation · Durability

See: [[Transactions]] · [[Deadlocks]]

---

## NULL Handling

```sql
WHERE col IS NULL              -- never: WHERE col = NULL
COALESCE(col, 'default')       -- first non-NULL value in the list
ISNULL(col, 'default')         -- SQL Server equivalent, two args only
```

`NOT IN` against a subquery that can return NULL silently matches nothing — prefer `NOT EXISTS`.

See: [[NULL and Three-Valued Logic]]

---

## String & Date Functions (common, syntax varies by engine)

| Task | Function |
|---|---|
| Concatenate | `CONCAT(a, b)` or `a \|\| b` |
| Lowercase / Uppercase | `LOWER(str)` / `UPPER(str)` |
| Substring | `SUBSTRING(str, start, length)` |
| Length | `LEN(str)` / `LENGTH(str)` |
| Current date/time | `GETDATE()` / `NOW()` / `CURRENT_TIMESTAMP` |
| Date part | `DATEPART(month, date)` / `EXTRACT(MONTH FROM date)` |
| Date math | `DATEADD(day, 7, date)` / `date + INTERVAL '7 days'` |

See: [[Combining Columns Into One String with CONCAT]] · [[Getting the Current Month Name]] · [[Calculating Age from a Birth Date]]

---

## Conditional Logic

```sql
CASE
	WHEN condition1 THEN result1
	WHEN condition2 THEN result2
	ELSE default_result
END
```

See: [[Bucketing Values Into Ranges With CASE]] · [[Updating Values Conditionally with CASE]]

---

## Views & Stored Procedures

```sql
CREATE VIEW ActiveCustomers AS
SELECT * FROM Customers WHERE Status = 'Active';

CREATE PROCEDURE GetOrdersByCustomer @CustomerID INT AS
BEGIN
	SELECT * FROM Orders WHERE CustomerID = @CustomerID;
END;
```

See: [[Views]] · [[Stored Procedures]]

---

## Performance Quick Checks

- Missing an index on a frequently filtered/joined column? → [[Missing Indexes]]
- Query slower than expected? → [[Slow Queries]], check the execution plan
- Two transactions stuck waiting on each other? → [[Deadlocks]]
- Leading-wildcard `LIKE '%x'` search won't use a normal index

See: [[Indexes]]

---

## 🔗 Related Notes

- [[SQL Codex]]
- [[PostgreSQL Cheat Sheet]]
