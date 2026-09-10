---
type: dax-pattern
status: published
created: 2026-09-10
updated: 2026-09-10
technology: Power BI
difficulty: Intermediate
tags:
  - dax
  - relationships
aliases:
  - RELATED Function
  - RELATEDTABLE
publish: true
---

# 🧩 Using RELATED Across a Relationship

> Pull a value from the "one" side of a relationship into the "many" side — the DAX equivalent of a [[INNER JOIN|join]], but for a calculated column.

---

## 🎯 Problem

A calculated column on the fact table (or "many" side) needs a value that actually lives on a related dimension table (the "one" side) — like pulling a product's category onto each sales row.

---

## 🤔 Mental Model

```text
DimProduct (one)  ──1:many──  FactSales (many)
   Category                      ProductID

RELATED(DimProduct[Category])
  → walks the relationship from the current FactSales row
    back to its matching DimProduct row, and returns that value
```

---

## 🧠 Why This Pattern Works

`RELATED()` only works in row context (calculated columns, or inside iterators) and only across a relationship where the current table is on the "many" side. It follows the relationship automatically — no manual join syntax needed, since the relationship already defines how the tables connect.

---

## 💻 DAX Solution

```dax
// Calculated column on FactSales
ProductCategory = RELATED(DimProduct[Category])
```

For the reverse direction — pulling values from the "many" side while sitting on the "one" side — use `RELATEDTABLE()` instead, which returns a full table rather than a single value:

```dax
// Calculated column or measure logic on DimProduct
OrderCount = COUNTROWS(RELATEDTABLE(FactSales))
```

---

## 🔄 Step-by-Step Breakdown

### Step 1: Confirm a relationship already exists
`RELATED()` relies entirely on an existing modeled relationship — see [[Data Modeling and Relationships]]. It can't substitute for a missing relationship.

### Step 2: Confirm which side of the relationship the current table sits on
`RELATED()` — current table is on the "many" side, pulling from the "one" side.
`RELATEDTABLE()` — current table is on the "one" side, pulling a full related table from the "many" side.

### Step 3: Reference the target column
```dax
RELATED(DimProduct[Category])
```
Returns the single matching value from the related table.

---

## 🚀 Common Use Cases

- Denormalizing a dimension attribute (category, region, department) directly onto a fact table row for easier filtering/grouping
- Counting related child rows from a parent row's perspective with `RELATEDTABLE()`
- Simplifying a calculated column that would otherwise need a lookup

---

## ⚖️ Alternatives

### `LOOKUPVALUE()` — works without a modeled relationship
```dax
ProductCategory =
LOOKUPVALUE(
	DimProduct[Category],
	DimProduct[ProductID], FactSales[ProductID]
)
```
Useful when no formal relationship exists between the tables, but generally slower and less idiomatic than `RELATED()` when a relationship is available.

### Handling the value in Power Query instead
For a value that's always needed together, merging it in during Power Query (see [[Merging Queries in Power Query]]) avoids a calculated column entirely and keeps the model leaner.

---

## ⚠️ Performance Considerations

- `RELATED()` is efficient when the relationship exists and is properly indexed by the model — it's a native, optimized lookup.
- `LOOKUPVALUE()` is a reasonable fallback but generally slower, since it doesn't leverage an existing relationship the same way.
- Using `RELATED()` inside a calculated column stores the resolved value on every row, adding to model size — consider whether the value is really needed as a stored column versus computed on demand.

---

## 🚨 Common Mistakes

- Trying to use `RELATED()` from the "one" side of a relationship (it only works from "many" to "one") — `RELATEDTABLE()` is needed for the reverse direction.
- Using `RELATED()` when no relationship actually exists between the two tables — it silently returns blank rather than an obvious error in some cases, or errors outright depending on context.
- Reaching for a calculated column with `RELATED()` when the same denormalization could have happened once, upstream, in Power Query.

---

## 💡 Wisdom from Mímir

If `RELATED()` isn't working, check the relationship's direction and cardinality first — see [[Data Modeling and Relationships]]. The function itself rarely has the bug; the modeled relationship underneath it usually does.

---

## 🔗 Related Notes

- [[Data Modeling and Relationships]]
- [[Calculated Columns vs Measures]]
