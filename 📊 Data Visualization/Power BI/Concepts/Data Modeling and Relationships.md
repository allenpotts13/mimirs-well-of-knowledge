---
type: concept
status: published
created: 2026-09-10
updated: 2026-09-10
technology: Power BI
difficulty: Intermediate
tags:
  - power-bi
  - data-modeling
  - relationships
aliases:
  - Power BI Relationships
  - Cardinality
publish: true
---

# 📚 Data Modeling and Relationships

> How tables connect to each other in the data model determines whether filtering one table correctly affects another — get this wrong and every visual downstream is quietly lying.

---

## 🎯 Purpose

Power BI's entire calculation engine relies on relationships between tables to know how a filter on one table should propagate to another. A wrong or missing relationship produces numbers that look plausible but are wrong.

---

## 🧠 Key Ideas

- A relationship links a column in one table to a column in another — almost always a key column, similar to a [[Foreign Keys|foreign key]] in a relational database.
- **Cardinality** describes the relationship shape: one-to-many (most common), one-to-one, or many-to-many.
- **Cross-filter direction** controls whether a filter flows one way (single) or both ways (bidirectional) between the two tables.
- Relationships can be **active** or **inactive** — only one active relationship can exist between two tables at a time; inactive ones need `USERELATIONSHIP()` in DAX to activate for a specific calculation.
- A well-modeled report uses a **star schema** — see [[Star Schema in Power BI]].

---

## ⚙️ How It Works

When a visual filters by a value in one table (say, selecting a date), Power BI follows the relationship lines outward to every table connected to it, filtering those too — as long as the relationship and its cross-filter direction allow it.

```text
DimDate (1) ─────< (many) FactSales
   filtering DimDate automatically filters FactSales
```

---

## 💻 Examples

```text
DimProduct (ProductID) ──1:many── FactSales (ProductID)
DimDate (DateKey)       ──1:many── FactSales (DateKey)
DimCustomer (CustomerID)──1:many── FactSales (CustomerID)
```

Selecting "2024" in a slicer on `DimDate` filters `FactSales` down to that year automatically, because of the relationship — no DAX needed for that part.

---

## 🚀 Real World Applications

- Connecting a fact table (sales, transactions) to its dimension tables (date, product, customer)
- Diagnosing why a slicer "isn't filtering" a particular visual
- Deciding whether a many-to-many relationship needs a bridge table instead

---

## ⚖️ Advantages

- Once modeled correctly, filtering propagates automatically — no manual DAX needed for basic slicing.
- A clean relationship structure makes the whole model easier to reason about and extend.

---

## ⚠️ Limitations

- Many-to-many relationships can produce ambiguous or double-counted results if not modeled carefully.
- Bidirectional filtering can cause circular filter paths and unpredictable results in complex models.
- Only one relationship between two tables can be active at once.

---

## 🚨 Common Mistakes

- Leaving a relationship's cross-filter direction as bidirectional "just in case," causing unexpected filtering in unrelated visuals.
- Building a many-to-many relationship directly instead of introducing a bridge table.
- Not noticing a relationship is inactive, and wondering why a slicer has no effect.
- Modeling a snowflake schema (dimension tables that reference other dimension tables) instead of flattening to a proper star schema.

---

## 📖 Further Reading

Documentation

Books

Videos

Articles

---

## 💡 Wisdom from Mímir

When a visual's numbers look wrong, check the relationships before checking the DAX. A measure can be written perfectly and still return the wrong number if the filter never actually reached the table it needed to.

---

## 🔗 Related Notes

- [[Star Schema in Power BI]]
- [[Power BI Building Blocks]]
- [[Calculated Columns vs Measures]]
