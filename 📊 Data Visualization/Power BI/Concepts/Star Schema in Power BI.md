---
type: concept
status: active
created: 2026-09-10
updated: 2026-09-10
technology: Power BI
difficulty: Intermediate
tags:
  - power-bi
  - data-modeling
  - star-schema
aliases:
  - Fact and Dimension Tables
---

# 📚 Star Schema in Power BI

> One fact table surrounded by dimension tables — the layout Power BI's engine is built to perform best on, and the layout DAX is easiest to reason about in.

---

## 🎯 Purpose

Power BI works best when the model mirrors a classic data warehouse star schema — it's not just a best practice, the engine (VertiPaq) and DAX's filter-propagation model are both designed around it.

---

## 🧠 Key Ideas

- **Fact table** — the large table of numeric, additive measurements (sales amount, quantity) plus foreign keys to every dimension.
- **Dimension table** — a smaller table of descriptive attributes (product name, customer region, calendar date) that the fact table can be sliced by.
- A **star schema** connects each dimension directly to the fact table — one hop, not nested through other dimensions.
- A **snowflake schema** normalizes dimensions further (a dimension referencing another dimension) — technically valid but not ideal for Power BI performance or DAX simplicity.
- A dedicated **date/calendar table**, marked as a Date table in the model, is close to mandatory for any time intelligence.

---

## ⚙️ How It Works

```text
        DimDate
            \
             \
DimProduct ─ FactSales ─ DimCustomer
             /
            /
        DimStore
```

Every dimension sits exactly one relationship away from the fact table. Filtering any dimension flows straight into the fact table with no intermediate hops.

---

## 💻 Examples

```text
FactSales(DateKey, ProductID, CustomerID, StoreID, SalesAmount, Quantity)
DimDate(DateKey, Year, Month, Quarter)
DimProduct(ProductID, ProductName, Category)
DimCustomer(CustomerID, CustomerName, Region)
DimStore(StoreID, StoreName, City)
```

Each `Dim*` table connects directly to `FactSales` on its key — no dimension connects to another dimension.

---

## 🚀 Real World Applications

- Structuring a new Power BI data model before importing any data
- Refactoring a flat, single-table import into a proper model for better performance and simpler DAX
- Explaining to a stakeholder why "just give me one big table" causes problems down the line

---

## ⚖️ Advantages

- Optimized for Power BI's in-memory engine — smaller dimension tables compress and filter efficiently.
- DAX measures stay simpler, since filter propagation follows short, predictable paths.
- Mirrors how most source data warehouses are already structured, easing the import.

---

## ⚠️ Limitations

- Takes more upfront modeling work than importing one flat table.
- Requires understanding relationships and cardinality correctly to avoid ambiguous filtering — see [[Data Modeling and Relationships]].

---

## 🚨 Common Mistakes

- Importing one giant denormalized table instead of splitting it into fact and dimension tables — this bloats the model size and makes DAX harder to write correctly.
- Snowflaking dimensions unnecessarily, adding extra hops the filter has to travel through.
- Forgetting to mark the date table as an official "Date table" in the model, breaking time intelligence functions.

---

## 📖 Further Reading

Documentation

Books

Videos

Articles

---

## 💡 Wisdom from Mímir

If a model feels slow or DAX measures feel unreasonably complicated, the schema is usually the actual problem, not the DAX. A clean star schema makes most measures nearly trivial to write; a flat or snowflaked model makes even simple totals fight the engine.

---

## 🔗 Related Notes

- [[Data Modeling and Relationships]]
- [[Power BI Building Blocks]]
- [[Building a Date Table]]
