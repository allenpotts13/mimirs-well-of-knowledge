---
type: concept
status: active
created: 2026-09-10
updated: 2026-09-10
technology: Power BI
difficulty: Advanced
tags:
  - power-bi
  - dax
aliases:
  - Filter Context
  - Row Context
  - DAX Context
---

# 📚 Row Context vs Filter Context

> The two "current environments" a DAX formula can be evaluated in — mixing them up is the source of nearly every confusing DAX result.

---

## 🎯 Purpose

DAX formulas never just compute a fixed answer — they compute an answer *for a specific context*. Understanding which kind of context a formula is running in explains why the same measure returns different numbers in different places.

---

## 🧠 Key Ideas

- **Row context** — exists when a formula is being evaluated one row at a time (calculated columns, and inside iterator functions like `SUMX`/`FILTER`). The formula "knows" which row it's currently on.
- **Filter context** — exists from everything currently filtering the data: slicers, visual axes, row/column headers in a table or matrix, and any `CALCULATE()` filters. Measures are always evaluated inside a filter context.
- Row context does **not** automatically apply filters to related tables — that requires **context transition**, which happens automatically inside `CALCULATE()`.
- A single formula can be inside both contexts at once — e.g. a measure used inside `SUMX` has both the iterator's row context and the surrounding filter context.

---

## ⚙️ How It Works

```text
Calculated column:  row context only
                     → knows the current row, nothing else

Measure:             filter context
                     → knows what's currently filtered (slicer,
                       visual, page, CALCULATE), not a specific row

SUMX / FILTER:       creates row context while iterating,
                     inside whatever filter context already existed
```

`CALCULATE()` is the bridge between the two — wrapping an expression in `CALCULATE()` converts the current row context into filter context, a process called **context transition**.

---

## 💻 Examples

```dax
// Row context — this column knows its own row's values directly
Profit = Sales[Revenue] - Sales[Cost]

// Filter context — this measure's result depends entirely on
// whatever is currently filtered when it's evaluated
Total Revenue = SUM(Sales[Revenue])

// SUMX creates row context, iterating row by row,
// inside whatever filter context already applies
Total Profit =
SUMX(
	Sales,
	Sales[Revenue] - Sales[Cost]
)
```

---

## 🚀 Real World Applications

- Explaining why a measure inside a table visual returns a different number per row (filter context changes per row/column of the visual)
- Writing correct iterator functions (`SUMX`, `AVERAGEX`, `FILTER`) that combine both kinds of context
- Debugging a measure that "works" in a card visual but breaks inside a matrix

---

## ⚖️ Advantages

- Once understood, context explains almost every DAX behavior that otherwise looks inconsistent.
- Enables extremely flexible, reusable measures — the same formula adapts automatically to whatever it's placed into.

---

## ⚠️ Limitations

- Genuinely one of the hardest concepts in Power BI to internalize — there's no equivalent concept in Excel or SQL to lean on.
- Easy to write a formula that "looks right" but silently behaves differently than intended because of a missed context transition.

---

## 🚨 Common Mistakes

- Assuming a calculated column's row context automatically filters related tables — it doesn't without `CALCULATE()` forcing context transition.
- Writing a measure and expecting it to behave like a calculated column — a measure has no fixed "row," only whatever context it's placed into.
- Nesting iterators without realizing each one introduces its own row context layered on top of the outer one.

---

## 📖 Further Reading

Documentation

Books

Videos

Articles

---

## 💡 Wisdom from Mímir

The clearest way I've found to build intuition: write the same measure, drop it into a card visual, then into a table broken out by category, then into a matrix broken out by two dimensions. Watching the *same formula* return different numbers in each place is what finally makes filter context click.

---

## 🔗 Related Notes

- [[DAX Fundamentals]]
- [[The CALCULATE Function]]
- [[Calculated Columns vs Measures]]
