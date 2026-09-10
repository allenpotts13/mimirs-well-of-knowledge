---
type: exam-scenario
status: published
created: 2026-09-10
updated: 2026-09-10
technology: Azure
difficulty: Beginner
tags:
  - dp-900
  - data-warehouse
  - star-schema
aliases:
  - Fact vs Dimension
publish: true
---

# 🧩 Star Schema - Fact Tables vs Dimension Tables

> *Every data warehouse question boils down to: is this column a number to measure, or context to describe it by?*

---

## 🎯 Problem

Given a list of columns or a scenario describing a warehouse table, classify each as belonging to a fact table or a dimension table.

---

## 🤔 Mental Model

```
        DimDate          DimProduct
            \                /
             \              /
              FactSales (the center)
             /              \
            /                \
      DimCustomer         DimStore
```

The fact table sits in the middle. Dimension tables radiate outward, each answering one descriptive question about the fact (when, what, who, where).

---

## 🧠 Why This Pattern Works

- **Fact table**: numeric, additive measures (sales amount, quantity, units) + foreign keys pointing to each dimension. Grows very large (one row per transaction/event).
- **Dimension table**: descriptive, mostly text attributes (product name, category, customer segment, calendar date). Stays comparatively small and changes slowly.

This split exists because OLAP queries almost always aggregate a measure (SUM of sales) *sliced by* some descriptive attribute (by month, by region) — separating the two makes that kind of query fast and the schema easy to reason about.

---

## 🔑 Decision Table

| Column example | Fact or Dimension? |
|---|---|
| `sales_amount`, `quantity_sold`, `discount_pct` | Fact (numeric, additive) |
| `product_name`, `product_category` | Dimension (DimProduct) |
| `order_date`, `fiscal_quarter` | Dimension (DimDate) |
| `customer_name`, `customer_region` | Dimension (DimCustomer) |
| `order_id` (as a measure of count) | Fact — can also be counted as a measure |

---

## 🚀 Common Use Cases

- Designing a warehouse schema from a list of source columns
- Explaining why a warehouse is denormalized compared to an OLTP source system
- Distinguishing star schema (one level of dimensions) from snowflake schema (dimensions normalized further into sub-dimensions)

---

## 🚨 Common Mistakes

- Calling a foreign key in the fact table a "measure" — it's a link to a dimension, not something you aggregate
- Assuming star schema and snowflake schema are the same thing — snowflake normalizes dimensions further, star schema keeps them flat/denormalized

---

## 💡 Wisdom from Mímir

If you'd naturally put the column in a `GROUP BY` clause, it's a dimension attribute. If you'd naturally put it in a `SUM()`/`AVG()`, it's a fact measure.

---

## 🔗 Related Notes

- [[Analytics Workloads on Azure]] — full concept writeup
- [[DP-900 Codex]]
