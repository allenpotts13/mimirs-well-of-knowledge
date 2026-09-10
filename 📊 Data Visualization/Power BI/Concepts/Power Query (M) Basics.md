---
type: concept
status: published
created: 2026-09-10
updated: 2026-09-10
technology: Power BI
difficulty: Beginner
tags:
  - power-bi
  - power-query
  - etl
aliases:
  - Power Query
  - M Language
publish: true
permalink: power-bi/power-query-m-basics
---

# <span class="rune">ᛟ</span> Power Query (M) Basics

> Power BI's ETL layer — clean, reshape, and combine data before it ever reaches the data model, using a point-and-click editor that writes M code behind the scenes.

---

## 🎯 Purpose

Power Query handles the "get the data into good shape" step — before modeling, before DAX. Anything wrong in the source data (bad types, extra columns, messy headers) should be fixed here, not patched around later with DAX.

---

## 🧠 Key Ideas

- Every transformation is recorded as a step in the **Applied Steps** list — fully re-orderable, editable, and re-playable on refresh.
- Under the hood, every transformation generates **M code** (Power Query's own functional language) — visible and editable in the Advanced Editor.
- Transformations are **non-destructive** to the source — the query defines a repeatable recipe, re-run against fresh data on every refresh.
- Common categories: shaping (pivot/unpivot, remove columns, filter rows), combining (merge, append), and cleaning (change type, trim, replace values).
- **Query folding** lets some transformations push down to the source system instead of running in Power BI itself — see [[Query Folding]].

---

## ⚙️ How It Works

```text
Source data → Applied Steps (recorded transformations) → clean output table
                     ↓
            regenerated as M code automatically
```

Each step in the Applied Steps pane wraps the previous step's result — reordering steps can change the outcome, since later steps operate on whatever the prior step produced.

---

## 💻 Examples

```m
let
	Source = Sql.Database("server", "database"),
	dbo_Sales = Source{[Schema="dbo",Item="Sales"]}[Data],
	FilteredRows = Table.SelectRows(dbo_Sales, each [SalesAmount] > 0),
	RenamedColumns = Table.RenameColumns(FilteredRows, {{"Amt", "SalesAmount"}})
in
	RenamedColumns
```

Every click in the Power Query UI (filter a row, rename a column) generates a line like this automatically.

---

## 🚀 Real World Applications

- Removing unnecessary columns before import to reduce model size
- Fixing inconsistent data types (text that should be numeric, dates stored as text)
- Combining multiple source files or tables into one clean table (see [[Merging Queries in Power Query]])

---

## ⚖️ Advantages

- Repeatable — the same cleanup automatically reapplies to every future refresh.
- No-code UI covers the vast majority of real-world cleanup needs.
- M code is available underneath for anything the UI can't express directly.

---

## ⚠️ Limitations

- Complex transformations can slow down refresh time, especially without query folding.
- The Applied Steps list can get long and hard to follow on a heavily-transformed query.
- M has its own syntax and functional style, distinct from DAX — a second language to learn.

---

## 🚨 Common Mistakes

- Doing cleanup work in DAX (with calculated columns) that should have been handled in Power Query instead — this bloats the model and duplicates logic.
- Reordering Applied Steps without checking whether a later step depends on the specific shape a prior step produced.
- Not checking whether a transformation breaks query folding, silently making refreshes much slower.

---

## 📖 Further Reading

Documentation

Books

Videos

Articles

---

## 💡 Wisdom from Mímir

If cleanup logic is being written twice — once as a Power Query step, once as a DAX workaround — that's a sign the Power Query step needs revisiting. Fix it once, upstream, and every downstream measure gets simpler automatically.

---

## 🔗 Related Notes

- [[Query Folding]]
- [[Merging Queries in Power Query]]
- [[Adding a Conditional Column in Power Query]]
