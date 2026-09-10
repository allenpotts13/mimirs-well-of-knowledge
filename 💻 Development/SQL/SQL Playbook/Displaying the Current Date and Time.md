---
type: query-pattern
status: published
created: 2026-08-02
updated: 2026-08-02
technology: SQL
difficulty: Beginner
tags:
  - dates
  - timestamps
  - sql-dialects
aliases:
  - Current Timestamp
  - Getting the Current Date and Time
publish: true
---

# <span class="rune">ᚲ</span> Displaying the Current Date and Time

> *Return the database system's current date and time.*

---

## 🎯 Problem

Display the current date and time from within a SQL query. 

This is useful when recording event times, generating reports, or testing date and time logic.

---

## 🧠 Why This Pattern Works

`CURRENT_TIMESTAMP` is a standard SQL expression that returns the current date and time according to the database session or server. 

It does not require a table because the value is supplied by the database system.

---

## 💻 SQL Solution

```sql
SELECT CURRENT_TIMESTAMP;
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 

The database evaluates: 

```sql 
CURRENT_TIMESTAMP
``` 

### Step 2 

It returns a timestamp similar to: 

```text 
2026-08-02 10:30:45.123456 
``` 

The exact format and precision depend on the database.

---

## 🧪 Common Variations 

### Current date only

```sql 
SELECT CURRENT_DATE; 
``` 

### Current time only 

```sql 
SELECT CURRENT_TIME; 
``` 

### PostgreSQL 

```sql 
SELECT NOW();
``` 
### SQL Server 

```sql 
SELECT GETDATE(); 
```

For greater precision: 

```sql 
SELECT SYSDATETIME(); 
```

### MySQL 

```sql 
SELECT NOW(); 
``` 

### Oracle 

```sql 
SELECT SYSTIMESTAMP FROM dual;
```

---
## 🚀 Common Use Cases

- Setting creation timestamps 
- Recording update times 
- Filtering recent records 
- Calculating elapsed time 
- Building scheduled reports 
- Auditing database activity

---

## ⚖️ Alternatives

Some databases provide multiple time functions that differ in:
- Precision 
- Time-zone handling 
- Whether the value reflects the transaction start time 
- Whether the value changes during a statement or transaction

Use the function appropriate for the database and the meaning required by the application.

---

## ⚠️ Performance Considerations

Calling the current timestamp is generally inexpensive. 

The greater concern is correctness: 
- Which time zone is being used? 
- Is the value based on the server, session, statement, or transaction? 
- Does the application expect UTC or local time?

---

## 🚨 Common Mistakes

- Assuming the database server uses the same time zone as the application. 
- Storing local time when UTC is required. 
- Treating a date as though it contains a time component. 
- Using a database-specific function in portable SQL. 
- Assuming all current-time functions behave identically inside a transaction.

---

## 💡 Wisdom from Mímir

For application data, prefer storing timestamps in UTC and convert them for display at the application or reporting layer. 

The difficult part is rarely retrieving the current time. The difficult part is ensuring that every system agrees on what that time represents.

---

## 🔗 Related Notes

- [[Filtering Records by a Recent Date Window]] — using the current date to build a relative filter (DATE_SUB/INTERVAL)
- [[Getting the Current Month Name]] — another CURDATE()-based lookup, for display rather than filtering
- [[Calculating Age from a Birth Date]] — comparing a stored date against the current date
- [[Getting the Day of the Week from a Date]] — another date-formatting/display function