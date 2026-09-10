---
type: dax-pattern
status: published
created: 2026-09-10
updated: 2026-09-10
technology: Power BI
difficulty: Beginner
tags:
  - dax
  - readability
  - performance
aliases:
  - VAR RETURN
  - DAX Variables
publish: true
---

# 🧩 Simplifying DAX with Variables

> Name intermediate results instead of repeating the same subexpression — the single easiest habit to make complex DAX readable and faster.

---

## 🎯 Problem

A DAX measure computes the same subexpression multiple times, or nests so many function calls that it's hard to read top to bottom, or trace what each piece is actually doing.

---

## 🤔 Mental Model

```dax
VAR StepOneResult = <expression>
VAR StepTwoResult = <expression using StepOneResult>
RETURN
	<final expression using StepOneResult and/or StepTwoResult>
```

Reads like a small script: compute named intermediate values first, then combine them in the final `RETURN` line.

---

## 🧠 Why This Pattern Works

Each `VAR` is evaluated exactly once, then reused by name as many times as needed in the `RETURN` expression — instead of Power BI recalculating the same subexpression repeatedly if it were written inline multiple times. This improves both readability and, often, performance.

---

## 💻 DAX Solution

```dax
YoY Growth % =
VAR CurrentSales = [Total Sales]
VAR PriorYearSales =
	CALCULATE([Total Sales], SAMEPERIODLASTYEAR(DimDate[Date]))
RETURN
	DIVIDE(CurrentSales - PriorYearSales, PriorYearSales)
```

Compare to the same logic without variables, repeating `[Total Sales]` and the prior-year calculation inline:

```dax
YoY Growth % (without variables) =
DIVIDE(
	[Total Sales] - CALCULATE([Total Sales], SAMEPERIODLASTYEAR(DimDate[Date])),
	CALCULATE([Total Sales], SAMEPERIODLASTYEAR(DimDate[Date]))
)
```

The second version computes the prior-year total twice and is noticeably harder to read.

---

## 🔄 Step-by-Step Breakdown

### Step 1: Identify repeated or complex subexpressions
Anything computed more than once, or any single line doing too much at once, is a candidate.

### Step 2: Name each one with VAR
```dax
VAR CurrentSales = [Total Sales]
```
Pick a name that describes what the value represents, not how it was computed.

### Step 3: Combine them in a final RETURN
```dax
RETURN DIVIDE(CurrentSales - PriorYearSales, PriorYearSales)
```
The `RETURN` line stays short and readable, since the heavy lifting already happened above it.

---

## 🚀 Common Use Cases

- Any measure referencing the same sub-measure or sub-calculation more than once
- Multi-step calculations (year-over-year growth, percent of total, ranking) where naming each step aids debugging
- Measures that are hard to read as one long nested expression

---

## ⚖️ Alternatives

### Breaking logic into separate helper measures instead
```dax
Prior Year Sales = CALCULATE([Total Sales], SAMEPERIODLASTYEAR(DimDate[Date]))
YoY Growth % = DIVIDE([Total Sales] - [Prior Year Sales], [Prior Year Sales])
```
Reusable across multiple other measures too, at the cost of adding more named objects to the model. Variables stay local to one measure; helper measures are visible and reusable everywhere.

---

## ⚠️ Performance Considerations

- Variables are evaluated once and cached for reuse within that measure — this can meaningfully improve performance versus repeating an expensive `CALCULATE()` call multiple times inline.
- No real performance cost to using variables liberally — there's little reason not to reach for them in anything beyond a one-line measure.

---

## 🚨 Common Mistakes

- Repeating the same expensive `CALCULATE()` expression multiple times inline instead of computing it once into a variable.
- Naming variables vaguely (`Var1`, `Temp`) instead of descriptively, undermining the readability benefit variables are supposed to provide.
- Forgetting `RETURN` is required — a `VAR` block without a final `RETURN` expression is invalid DAX.

---

## 💡 Wisdom from Mímir

Once a measure needs more than one nested function call or references the same sub-calculation twice, that's the signal to reach for `VAR`/`RETURN`. It costs nothing and pays off the first time that measure needs to be debugged six months later.

---

## 🔗 Related Notes

- [[DAX Fundamentals]]
- [[Calculating Year-over-Year Growth]]
