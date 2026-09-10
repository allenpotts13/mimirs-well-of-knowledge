---
type: concept
status: active
created: 2026-09-10
updated: 2026-09-10
technology: Power BI
difficulty: Advanced
tags:
  - power-bi
  - power-query
  - performance
aliases:
  - Query Fold
---

# 📚 Query Folding

> Power Query pushing transformation steps down to the source system to run there, instead of pulling raw data into Power BI first and transforming it locally.

---

## 🎯 Purpose

Query folding is the difference between a refresh that finishes in seconds and one that takes minutes — it determines whether a database does the filtering/sorting work, or Power BI has to pull everything and do it after the fact.

---

## 🧠 Key Ideas

- Applies mainly to database-backed sources (SQL Server, PostgreSQL, etc.) that can translate M steps into their own native query language (like SQL).
- As long as folding continues, each Applied Step is translated into part of the source query — nothing extra is transferred over the network.
- Certain transformations **break folding** — once broken, every subsequent step runs locally in Power BI's engine on the full unfiltered dataset already pulled down.
- File-based sources (Excel, CSV, flat files) generally can't fold at all — there's no query engine on the other end to push work to.

---

## ⚙️ How It Works

```text
Folding:      Power BI translates steps → SQL runs on the server →
              only the filtered/aggregated result crosses the network

Not folding:  Power BI pulls the full raw table →
              transformations run locally, row by row, in Power Query's engine
```

Right-clicking a step and checking "View Native Query" (when available) confirms folding is still active up to that point.

---

## 💻 Examples

```text
Folds:
- Filtering rows (Table.SelectRows)
- Removing columns
- Simple renames
- Grouping/aggregating
- Sorting

Often breaks folding:
- Custom M functions
- Adding an index column
- Changing data types in certain complex ways
- Merging queries from two different source types
- Using Table.Buffer explicitly
```

---

## 🚀 Real World Applications

- Diagnosing a slow refresh against a database source
- Deciding whether to filter early in the query (to preserve folding) or later
- Choosing between DirectQuery and Import partly based on whether the needed transformations can fold — see [[Storage Modes - Import vs DirectQuery vs Live Connection]]

---

## ⚖️ Advantages

- Dramatically reduces data transferred and refresh time when it works.
- Lets the source database do what it's already optimized for — filtering and aggregating large tables.

---

## ⚠️ Limitations

- Once broken, it stays broken for every subsequent step — order matters.
- Not available at all for many file-based and API sources.
- Hard to predict without checking — some transformations that look simple silently break folding.

---

## 🚨 Common Mistakes

- Adding transformation steps in an order that breaks folding early, then doing everything else on the full unfiltered table.
- Assuming folding always applies to database sources — it depends on the specific connector and transformation, not just the source type.
- Not checking "View Native Query" when a refresh is unexpectedly slow.

---

## 📖 Further Reading

Documentation

Books

Videos

Articles

---

## 💡 Wisdom from Mímir

Put filtering steps as early as possible in the query — before anything that might break folding. Even one early filter step that folds can shrink everything downstream from millions of rows to thousands, regardless of what happens to folding after that point.

---

## 🔗 Related Notes

- [[Power Query (M) Basics]]
- [[Storage Modes - Import vs DirectQuery vs Live Connection]]
