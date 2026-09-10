---
type: concept
status: active
created: 2026-09-10
updated: 2026-09-10
technology: Power BI
difficulty: Beginner
tags:
  - power-bi
  - dax
aliases:
  - Data Analysis Expressions
---

# 📚 DAX Fundamentals

> The formula language behind every measure and calculated column in Power BI — closer to Excel formulas than to SQL, but with its own distinct rules.

---

## 🎯 Purpose

DAX (Data Analysis Expressions) is how Power BI computes anything beyond raw imported data — totals, ratios, rankings, time comparisons. Every measure and calculated column is written in it.

---

## 🧠 Key Ideas

- Functions look similar to Excel (`SUM`, `IF`, `AVERAGE`) but operate over entire columns/tables, not individual cells.
- DAX always operates within a **context** — either row context or filter context — which determines what data a formula actually "sees." See [[Row Context vs Filter Context]].
- Table functions (`FILTER`, `ALL`, `VALUES`, `SUMMARIZE`) return whole tables, not single values, and are frequently nested inside other functions.
- `CALCULATE()` is the single most important function in DAX — it modifies the filter context a formula runs in. See [[The CALCULATE Function]].
- Variables (`VAR`/`RETURN`) make complex DAX readable and avoid repeating the same subexpression multiple times.

---

## ⚙️ How It Works

A DAX formula doesn't just compute a static result — it computes a result *for the current context* (whatever row, filter, or visual it's being evaluated inside). The same measure can return a different number in every cell of a matrix visual, because each cell has a different filter context.

```dax
Total Sales = SUM(Sales[SalesAmount])
```

In a table visual broken out by Region, this one measure definition automatically returns a different total for every region row — DAX re-evaluates it per context, not once globally.

---

## 💻 Examples

```dax
Total Sales = SUM(Sales[SalesAmount])

Total Orders = COUNTROWS(Sales)

Average Order Value = DIVIDE([Total Sales], [Total Orders])

Sales Category =
IF(
	Sales[SalesAmount] > 1000,
	"Large",
	"Small"
)
```

---

## 🚀 Real World Applications

- Any KPI: total revenue, order count, average basket size
- Conditional categorization of rows into buckets
- Building the underlying logic behind every chart and card visual in a report

---

## ⚖️ Advantages

- Purpose-built for the kind of aggregate, context-aware calculations reports need.
- Familiar syntax for anyone coming from Excel formulas.
- Extremely composable — measures can reference other measures.

---

## ⚠️ Limitations

- The context model (row vs. filter context) is genuinely different from how Excel or SQL work, and takes real time to internalize.
- Performance can degrade quickly with poorly written, deeply nested table functions on large models.

---

## 🚨 Common Mistakes

- Writing DAX like Excel formulas without understanding that context — not cell position — determines the result.
- Using `SUM(Column)` when a `DIVIDE()` or ratio measure was actually needed, causing division-by-zero errors instead of a clean blank.
- Not using variables in longer formulas, making them unreadable and repeating expensive calculations.

---

## 📖 Further Reading

Documentation

Books

Videos

Articles

---

## 💡 Wisdom from Mímir

DAX confusion is almost always a context confusion, not a syntax error. When a measure returns an unexpected number, the first question is always "what filter context is this actually running in right now" — not "is my formula wrong."

---

## 🔗 Related Notes

- [[Row Context vs Filter Context]]
- [[The CALCULATE Function]]
- [[Calculated Columns vs Measures]]
- [[Simplifying DAX with Variables]]
