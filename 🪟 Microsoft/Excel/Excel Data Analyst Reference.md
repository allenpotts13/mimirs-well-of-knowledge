---
publish: true
status: published
permalink: excel/excel-data-analyst-reference
description: "The 30 Excel formulas and functions every data analyst should know, with a one-line explanation beside each."
---

# <span class="rune">ᛊ</span> Excel Data Analyst Reference

> Thirty formulas cover the overwhelming majority of everyday analyst work in Excel — lookups, conditional aggregation, text cleanup, and date handling. ★ marks the ones reached for most often.

---

## The 30 Functions Every Data Analyst Should Know

| # | Function | What It Does |
|---:|---|---|
| 1 | `SUM()` ★ | Adds a range of numbers |
| 2 | `AVERAGE()` | Calculates the mean of a range |
| 3 | `IF()` | Returns one value if a condition is true, another if false |
| 4 | `COUNT()` | Counts cells containing numbers |
| 5 | `COUNTA()` | Counts non-empty cells, regardless of content type |
| 6 | `COUNTIF()` | Counts cells matching one condition |
| 7 | `COUNTIFS()` | Counts cells matching multiple conditions at once |
| 8 | `SUMIF()` | Sums cells matching one condition |
| 9 | `SUMIFS()` | Sums cells matching multiple conditions at once |
| 10 | `VLOOKUP()` | Looks up a value in the first column of a range and returns a value from a specified column to its right |
| 11 | `HLOOKUP()` | Same idea as VLOOKUP, but searches horizontally across a row instead of down a column |
| 12 | `XLOOKUP()` ★ | Modern, flexible lookup that replaces VLOOKUP/HLOOKUP/INDEX-MATCH — searches in any direction, no column-counting required |
| 13 | `INDEX()` | Returns the value at a given row/column position within a range |
| 14 | `MATCH()` | Returns the position of a value within a range — commonly paired with INDEX for flexible lookups |
| 15 | `CONCAT()` | Joins multiple text strings or cell values together into one |
| 16 | `LEFT()` | Returns a specified number of characters from the start of a string |
| 17 | `RIGHT()` | Returns a specified number of characters from the end of a string |
| 18 | `MID()` | Returns characters from the middle of a string, given a starting position and length |
| 19 | `LEN()` | Returns the number of characters in a string |
| 20 | `TRIM()` | Removes extra/leading/trailing spaces from text |
| 21 | `UPPER()` | Converts text to all uppercase |
| 22 | `LOWER()` | Converts text to all lowercase |
| 23 | `PROPER()` | Capitalizes the first letter of each word |
| 24 | `TEXT()` | Formats a number or date as text using a specified display format |
| 25 | `ROUND()` | Rounds a number to a specified number of digits |
| 26 | `ROUNDUP()` | Rounds a number up, away from zero |
| 27 | `ROUNDDOWN()` | Rounds a number down, toward zero |
| 28 | `TODAY()` | Returns the current date |
| 29 | `NOW()` | Returns the current date and time |
| 30 | `SUBTOTAL()` | Performs an aggregate calculation (sum, average, count, etc.) that automatically ignores other SUBTOTAL results and hidden/filtered rows |

---

## 🚀 Where These Show Up Most

- **Lookups** (`VLOOKUP`, `XLOOKUP`, `INDEX`/`MATCH`) — pulling a related value from another table, the single most common analyst task in Excel
- **Conditional aggregation** (`COUNTIF(S)`, `SUMIF(S)`) — summarizing data that matches specific criteria without building a separate PivotTable every time
- **Text cleanup** (`TRIM`, `PROPER`, `LEFT`/`RIGHT`/`MID`) — standardizing messy imported data (inconsistent casing, extra whitespace, combined fields that need splitting)
- **Filtered totals** (`SUBTOTAL`) — the correct choice inside a filtered table or list, since a plain `SUM` would still include hidden rows

---

## 🔗 Related Notes

- [[Excel Codex]]
- [[SQL Data Analyst Reference]]
- [[Power BI Data Analyst Reference]]
- [[Python Data Analyst Reference]]
