---
type: power-query-pattern
status: published
created: 2026-09-10
updated: 2026-09-10
technology: Power BI
difficulty: Intermediate
tags:
  - power-query
  - merge
aliases:
  - Power Query Merge
  - Merge Queries
publish: true
---

# <span class="rune">ᚲ</span> Merging Queries in Power Query

> Power Query's version of a [[INNER JOIN|join]] — combine columns from two queries based on matching key values, before the data ever reaches the model.

---

## 🎯 Problem

Two separate queries (tables) need to be combined into one — pulling columns from a lookup/reference table onto the main table, based on a shared key.

---

## 🤔 Mental Model

```text
Sales (main query)          Products (lookup query)
  ProductID                    ProductID, ProductName, Category
       ↓ merge on ProductID
Sales + ProductName + Category, all in one table
```

---

## 🧠 Why This Pattern Works

A merge matches rows between two queries on one or more key columns, similar to a SQL join. The result is a new column containing a nested table of matches, which is then expanded to pull specific columns out flat onto the main query.

---

## 🔄 Power Query Steps

### Step 1: Start from the main query, choose Merge Queries
`Home` → `Merge Queries` (or `Merge Queries as New` to keep the original query untouched).

### Step 2: Select the second query and matching key column(s)
Choose the lookup query and click the matching key column in both tables.

### Step 3: Choose the join kind
| Join kind | Power Query equivalent of |
|---|---|
| Left Outer | [[LEFT JOIN]] |
| Right Outer | [[RIGHT JOIN]] |
| Inner | [[INNER JOIN]] |
| Full Outer | [[FULL OUTER JOIN]] |
| Left/Right Anti | rows with **no** match — like a LEFT JOIN + `WHERE right.key IS NULL` |

### Step 4: Expand the resulting nested column
Click the expand icon on the new merged column, and choose which specific columns from the lookup query to bring in.

---

## 💻 Underlying M Code

```m
let
	Source = Sales,
	MergedWithProducts = Table.NestedJoin(
		Source, {"ProductID"},
		Products, {"ProductID"},
		"ProductsMatch", JoinKind.LeftOuter
	),
	ExpandedColumns = Table.ExpandTableColumn(
		MergedWithProducts, "ProductsMatch", {"ProductName", "Category"}
	)
in
	ExpandedColumns
```

---

## 🚀 Common Use Cases

- Pulling a category, region, or friendly label from a lookup/dimension table onto the main fact table
- Combining data from two different source systems that share a common key
- Building a denormalized query intentionally, before loading into the model — see [[Star Schema in Power BI]] for when *not* to do this

---

## ⚖️ Alternatives

### `RELATED()` in DAX, after loading both tables separately
```dax
ProductCategory = RELATED(Products[Category])
```
Keeps both tables separate in the model (often the better choice for a star schema) instead of pre-flattening them in Power Query — see [[Using RELATED Across a Relationship]].

### `Table.Combine` / Append for stacking rows instead of columns
Merging combines columns side-by-side; appending (see the Append Queries feature) stacks rows from tables with the same structure — a different operation entirely.

---

## ⚠️ Performance Considerations

- A merge against a database source can break [[Query Folding]] depending on the source types and connectors involved — worth checking "View Native Query" afterward.
- Merging early, before other transformations, sometimes preserves folding better than merging as a late step — behavior varies by connector.

---

## 🚨 Common Mistakes

- Merging when the relationship should really stay as a proper model relationship instead — flattening everything in Power Query works against the star schema pattern and bloats the resulting table.
- Choosing the wrong join kind (e.g. Inner instead of Left Outer), silently dropping rows that had no match.
- Forgetting to expand only the needed columns, accidentally pulling in every column from the lookup query.

---

## 💡 Wisdom from Mímir

Before merging, ask whether this data really needs to be flattened into one wide table, or whether it should stay as two separate model tables connected by a relationship. Merging is right for combining data from genuinely different sources; it's often the wrong tool when the real answer is [[Data Modeling and Relationships|a proper relationship]] in the model instead.

---

## 🔗 Related Notes

- [[Power Query (M) Basics]]
- [[Query Folding]]
- [[Using RELATED Across a Relationship]]
