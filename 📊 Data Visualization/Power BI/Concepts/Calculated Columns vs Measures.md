---
type: concept
status: active
created: 2026-09-10
updated: 2026-09-10
technology: Power BI
difficulty: Intermediate
tags:
  - power-bi
  - dax
  - data-modeling
aliases:
  - Calculated Column
  - Measure
---

# 📚 Calculated Columns vs Measures

> Both are built with DAX, but one is computed once and stored per row; the other is computed on the fly, per visual, in response to whatever's currently filtered.

---

## 🎯 Purpose

Mixing these up is the single most common DAX mistake for people coming from Excel. Picking the wrong one leads to either bloated model size or numbers that don't respond to filtering the way expected.

---

## 🧠 Key Ideas

- **Calculated column** — evaluated once per row, at data refresh time, and stored physically in the model (adds to model size).
- **Measure** — evaluated on demand, in the context of whatever's currently filtered (slicers, rows, columns in a visual) — never stored.
- Calculated columns can be used as slicers, in `GROUP BY`-style groupings, and on axes — measures generally can't (they need row context, which visuals provide, but a slicer doesn't).
- Measures automatically respond to filter context; calculated columns are frozen at their row's value regardless of what's filtered.

---

## ⚙️ How It Works

```text
Calculated column:  computed once per row, at refresh
                     Profit = Sales[Revenue] - Sales[Cost]

Measure:             computed fresh, every time, for the current filter context
                     Total Profit = SUM(Sales[Revenue]) - SUM(Sales[Cost])
```

A calculated column's value never changes when a user clicks a slicer — it's baked in. A measure recalculates completely every time the filter context changes.

---

## 💻 Examples

```dax
// Calculated column — one static value per row
FullName = Employees[FirstName] & " " & Employees[LastName]

// Measure — recalculates based on filter context
Total Sales = SUM(Sales[SalesAmount])

Average Order Value =
DIVIDE(
	[Total Sales],
	DISTINCTCOUNT(Sales[OrderID])
)
```

---

## 🚀 Real World Applications

- Calculated column: concatenating a name, categorizing a value into a bucket for use as a slicer field
- Measure: total sales, average order value, any KPI that needs to respond to whatever's currently filtered

---

## ⚖️ Advantages

**Calculated columns**
- Can be used directly as slicer/axis fields.
- Simple row-by-row logic is easy to reason about.

**Measures**
- Always reflect the current filter context — no stale values.
- Don't bloat model size, since nothing is stored.

---

## ⚠️ Limitations

**Calculated columns**
- Increase model size and refresh time — every row stores the computed value.
- Frozen at refresh time; won't adapt to interactive filtering the way a measure does.

**Measures**
- Can't be used as a slicer or axis field, since they have no value without a filter context to evaluate against.
- Slightly more DAX complexity to write correctly (understanding filter context).

---

## 🚨 Common Mistakes

- Building a calculated column to compute a total that should respond to filtering — the number then looks "stuck" and doesn't update when a slicer changes.
- Defaulting to calculated columns out of Excel habit, bloating the model when a measure would do the same job without the storage cost.
- Trying to put a measure on a slicer or axis and being confused why it isn't allowed.

---

## 📖 Further Reading

Documentation

Books

Videos

Articles

---

## 💡 Wisdom from Mímir

Ask one question: "does this value need to change based on what the user has filtered or selected?" If yes, it's a measure. If it's a fixed, row-level fact that should exist regardless of any filter (like a full name or a static category), it's a calculated column.

---

## 🔗 Related Notes

- [[DAX Fundamentals]]
- [[Row Context vs Filter Context]]
- [[Data Modeling and Relationships]]
