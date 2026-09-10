---
publish: true
status: published
---

# <span class="rune">ᚢ</span> SQL

> "Data is only as valuable as your ability to retrieve it."

---

## 📚 Concepts

Foundational theory — one idea per note.

### Joins
- [[INNER JOIN]]
- [[LEFT JOIN]]
- [[RIGHT JOIN]]
- [[FULL OUTER JOIN]]
- [[CROSS JOIN]]

### Keys & Constraints
- [[Primary Keys]]
- [[Foreign Keys]]
- [[Constraints]]

### Schema Design
- [[Normalization]]

### Query Structure
- [[Query Execution Order]]
- [[Subqueries]]
- [[GROUP BY and HAVING]]
- [[UNION vs UNION ALL]]
- [[Aggregate Functions]]
- [[NULL and Three-Valued Logic]]
- [[DDL vs DML vs DCL vs TCL]]

### Performance & Reliability
- [[Indexes]]
- [[Transactions]]
- [[Window Functions]]

### Database Objects
- [[Views]]
- [[Stored Procedures]]

---

## 🧩 Playbook — Query Patterns

Task-oriented, one pattern per note. This is the bulk of the SQL knowledge base.

### Filtering & Pattern Matching
- [[Filtering Records by Year]]
- [[Filtering Records by a Recent Date Window]]
- [[Filtering Rows by Email Domain]]
- [[Filtering Rows With a NULL Column]]
- [[Filtering Values That Are a Multiple of a Number]]
- [[Filtering Employees by Length of Tenure]]
- [[Finding Values Within a Range]]
- [[Finding Values That Start With a Pattern]]
- [[Matching Text Patterns with LIKE]]

### Aggregation & Grouping
- [[Calculating a Total with SUM()]]
- [[Calculating Multiple Aggregates Per Group]]
- [[Calculating Each Group's Percentage of the Total]]
- [[Aggregating Values Within a Date Range]]
- [[Bucketing Values Into Ranges With CASE]]
- [[Counting Employees in Each Department]]
- [[Find the Total Number of Departments]]
- [[Find the Department with the Lowest Average Salary]]
- [[Finding the Group With the Highest Count]]
- [[Finding the Highest Salary in Each Department]]
- [[Finding Groups Below a Size Threshold]]
- [[Finding Groups With No Recent Activity]]
- [[Finding Values Above the Average]]
- [[Filtering Rows Above Their Group's Average]]
- [[Finding Values in a Statistical Percentile]]

### Ranking & Top-N
- [[Finding the Nth Highest Value]]
- [[Finding the Second Highest Value]]
- [[Ranking the Top N Rows Within Each Group]]
- [[Retrieving the Top N Highest Values]]
- [[Retrieving the Last N Records]]
- [[How to Fetch the First and Last Record from a Table]]
- [[Finding the Youngest Employee]]

### Self-Joins & Hierarchical Data
- [[Comparing Related Rows With a Self-Join]]
- [[Find All Employees Who Report to a Specific Manager]]
- [[Finding Employees Who Earn More Than Their Manager]]
- [[Finding Employees With No Subordinates]]
- [[Finding Peers Who Share the Same Manager]]

### Set Comparisons & Duplicates
- [[Finding Common Records Between Two Tables]]
- [[Finding Duplicate Records]]
- [[Deleting Duplicate Rows]]
- [[Finding Records That Match Every Value in a Set]]
- [[Finding Records With a Missing Relationship]]
- [[Checking Whether a Table Is Empty]]

### String & Text Functions
- [[Combining Columns Into One String with CONCAT]]
- [[Comparing the First and Last Character of a String]]
- [[Converting Text to Lowercase]]

### Date & Time Functions
- [[Calculating Age from a Birth Date]]
- [[Displaying the Current Date and Time]]
- [[Getting the Current Month Name]]
- [[Getting the Day of the Week from a Date]]

### Data Modification
- [[Delete All Employees from a Department]]
- [[Emptying a Table with TRUNCATE]]
- [[Increasing Every Employee's Salary by 10%]]
- [[Updating Values Conditionally with CASE]]

### Exporting
- [[Exporting Query Results as XML]]

---

## 💻 Query Examples

- [[SELECT Examples]]
- [[UPDATE with JOIN]]
- [[MERGE Statement]]
- [[Common Table Expressions]]

---

## 🚑 Troubleshooting

- [[Deadlocks]]
- [[Slow Queries]]
- [[Missing Indexes]]

---

## ⚡ Cheat Sheets

- [[SQL Cheat Sheet]]
- [[PostgreSQL Cheat Sheet]]

---

## 🚀 Related Projects

- Inventory App
- TEIS
- Motorcycle Dashboard