---
type: query-pattern
status: published
created: 2026-09-02
updated: 2026-09-02
technology: SQL
difficulty: Beginner
tags:
  - query-pattern
  - xml
  - export
  - sql-server
aliases:
  - FOR XML AUTO
publish: true
permalink: sql/exporting-query-results-as-xml
---

# <span class="rune">ᚲ</span> Exporting Query Results as XML

> *Shape a result set directly into XML using SQL Server's FOR XML clause.*

---

## 🎯 Problem

How do you get all employee records back from a query already formatted as XML instead of a plain result set?

---

## 💻 SQL Solution

```sql
SELECT employee_id, name, department_id
FROM employees
FOR XML AUTO;
```

---

## 📝 Notes

- **This is SQL Server syntax, not MySQL.** The rest of this playbook targets MySQL, but `FOR XML` is a T-SQL-only clause and will not run against a MySQL server.
- MySQL has no native `FOR XML` equivalent. If you need XML output from MySQL, that transformation is typically handled at the application layer (e.g. your ORM, reporting tool, or export script) rather than inside the query itself.
- `FOR XML AUTO` lets SQL Server infer the element nesting from your table/column structure; `FOR XML PATH` and `FOR XML RAW` give more explicit control if you're actually on SQL Server.
- If you're stuck needing MySQL to emit XML-ish text directly, `CONCAT()` can hand-build tags per row, though this is a workaround, not a real serialization feature — see [[Combining Columns Into One String with CONCAT]].

---

## 🔗 Related Notes

- [[Combining Columns Into One String with CONCAT]]
