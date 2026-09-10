---
type: dax-pattern
status: published
created: 2026-09-10
updated: 2026-09-10
technology: Power BI
difficulty: Beginner
tags:
  - dax
  - null-handling
aliases:
  - DAX BLANK
  - DIVIDE Function
publish: true
permalink: power-bi/handling-blank-values-with-dax
---

# <span class="rune">ᚲ</span> Handling Blank Values with DAX

> BLANK() is DAX's version of NULL — and DIVIDE() is the safe way to avoid it turning into a visible error.

---

## 🎯 Problem

A measure needs to gracefully show nothing (or a specific fallback) instead of an error or a misleading zero, when the underlying data has no value to work with — most commonly during division.

---

## 🤔 Mental Model

```text
Regular division:  5 / 0        → error, visible and ugly
DIVIDE() version:   DIVIDE(5,0) → blank, clean and quiet
```

---

## 🧠 Why This Pattern Works

`BLANK()` is DAX's representation of "no value" — similar in spirit to SQL's `NULL`, see [[NULL and Three-Valued Logic]]. `DIVIDE()` is purpose-built to return `BLANK()` (or an optional fallback value) instead of throwing a division-by-zero error, which raw `/` division does not do safely.

---

## 💻 DAX Solution

```dax
// Safe division — returns BLANK() instead of erroring on zero
Profit Margin % =
DIVIDE(
	[Total Profit],
	[Total Sales]
)

// Safe division with an explicit fallback instead of blank
Profit Margin % (with fallback) =
DIVIDE(
	[Total Profit],
	[Total Sales],
	0
)

// Replacing a blank measure result with a friendly value
Sales Display =
IF(
	ISBLANK([Total Sales]),
	"No Data",
	FORMAT([Total Sales], "$#,##0")
)
```

---

## 🔄 Step-by-Step Breakdown

### Step 1: Identify where blanks/division can occur
Usually anywhere a ratio or percentage is computed, or wherever a measure depends on a related table that might have no matching rows.

### Step 2: Wrap risky division in DIVIDE()
```dax
DIVIDE(numerator, denominator)
```
Returns `BLANK()` automatically instead of an error when the denominator is zero.

### Step 3: Optionally handle blanks explicitly with ISBLANK() or COALESCE()
```dax
IF(ISBLANK([Measure]), "No Data", [Measure])
COALESCE([Measure], 0)
```
Use when a blank needs to be replaced with a specific fallback value or label for display.

---

## 🚀 Common Use Cases

- Any percentage or ratio measure (margin %, growth %, conversion rate)
- Displaying "No Data" instead of a blank card when a filter selection has no matching rows
- Preventing a visual from showing a jarring error message to end users

---

## ⚖️ Alternatives

### Raw division with a manual zero-check
```dax
IF([Total Sales] = 0, BLANK(), [Total Profit] / [Total Sales])
```
Works, but `DIVIDE()` is shorter, harder to get wrong, and the idiomatic choice.

### `COALESCE()` for a general blank-replacement, not just division
```dax
COALESCE([Total Sales], 0)
```
Replaces a blank result of any kind (not just division) with a fallback value.

---

## ⚠️ Performance Considerations

- `DIVIDE()` has negligible overhead compared to raw division — there's no performance reason to avoid it.
- Excessive `ISBLANK()` checks wrapping many measures can add some evaluation overhead in very large, complex reports, but is rarely a real bottleneck.

---

## 🚨 Common Mistakes

- Using raw `/` division for any ratio measure, risking a visible error the first time the denominator is zero (a new category with no data yet, for example).
- Confusing a blank result with a zero result — `BLANK()` means "nothing to show," a `0` is a real, meaningful value; treating them the same can misrepresent the data.
- Forgetting that `SUM()` of an entirely blank column returns `BLANK()`, not `0` — this affects how downstream comparisons and conditional formatting behave.

---

## 💡 Wisdom from Mímir

Default to `DIVIDE()` for every ratio in a model, without exception — see [[Calculating Year-over-Year Growth]]. It's one habit that prevents an entire category of "why does this visual have a red error triangle" support requests.

---

## 🔗 Related Notes

- [[NULL and Three-Valued Logic]]
- [[DAX Fundamentals]]
- [[Calculating Year-over-Year Growth]]
