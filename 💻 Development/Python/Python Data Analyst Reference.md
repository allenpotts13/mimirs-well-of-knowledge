---
publish: true
status: published
permalink: python/python-data-analyst-reference
description: "The 30 pandas functions and attributes every data analyst should know, with a one-line explanation beside each."
---

# <span class="rune">ᛊ</span> Python Data Analyst Reference

> Thirty pandas functions and attributes cover the overwhelming majority of everyday analyst work — loading data, inspecting it, cleaning it, and reshaping it. ★ marks the functions at the very start and end of nearly every analysis. See the full [[Python Codex|Python Codex]] for core language depth.

---

## The 30 Functions Every Data Analyst Should Know

| # | Function | What It Does |
|---:|---|---|
| 1 | `pd.read_csv()` ★ | Reads a CSV file into a DataFrame |
| 2 | `pd.read_excel()` | Reads an Excel file into a DataFrame |
| 3 | `.head()` | Returns the first N rows of a DataFrame (default 5) |
| 4 | `.tail()` | Returns the last N rows of a DataFrame (default 5) |
| 5 | `.info()` | Summarizes a DataFrame's columns, data types, and non-null counts |
| 6 | `.describe()` | Generates summary statistics (mean, std, quartiles) for numeric columns |
| 7 | `.shape` | Returns the (rows, columns) dimensions of a DataFrame |
| 8 | `.columns` | Returns the column labels of a DataFrame |
| 9 | `.dtypes` | Returns the data type of every column |
| 10 | `.rename()` | Renames columns or index labels |
| 11 | `.isnull()` | Flags missing (NaN) values as True/False |
| 12 | `.notnull()` | Flags non-missing values as True/False |
| 13 | `.dropna()` | Removes rows or columns containing missing values |
| 14 | `.fillna()` | Replaces missing values with a specified value |
| 15 | `.groupby()` | Groups rows by one or more columns, in preparation for aggregation |
| 16 | `.agg()` | Applies one or more aggregate functions to grouped data |
| 17 | `.sort_values()` | Sorts a DataFrame by one or more columns |
| 18 | `.value_counts()` | Counts the occurrences of each unique value in a column |
| 19 | `.merge()` | Combines two DataFrames based on a shared column — the pandas equivalent of a SQL join |
| 20 | `.join()` | Combines DataFrames based on their index rather than a column |
| 21 | `pd.concat()` | Stacks DataFrames together, vertically or horizontally |
| 22 | `.apply()` | Applies a custom function along an axis (rows or columns) of a DataFrame |
| 23 | `.map()` | Applies a function or mapping element-wise to a Series |
| 24 | `.pivot_table()` | Reshapes data into a spreadsheet-style pivot summary |
| 25 | `pd.crosstab()` | Computes a cross-tabulation (frequency table) between two or more columns |
| 26 | `.nunique()` | Counts the number of distinct values in a column |
| 27 | `.unique()` | Returns the actual distinct values in a column |
| 28 | `.astype()` | Converts a column to a specified data type |
| 29 | `pd.to_datetime()` | Converts a column to proper datetime type |
| 30 | `.to_csv()` ★ | Writes a DataFrame out to a CSV file |

---

## 🚀 Where These Show Up Most

- **Loading and inspecting** (`read_csv`, `head`, `info`, `describe`) — the first four commands run on almost any new dataset, in that order
- **Cleaning** (`isnull`, `dropna`, `fillna`, `astype`) — handling missing data and fixing incorrectly-typed columns before any real analysis begins
- **Grouping and aggregating** (`groupby`, `agg`, `pivot_table`) — the pandas equivalent of a SQL `GROUP BY` or an Excel PivotTable, and the core of most reporting work
- **Combining data** (`merge`, `concat`) — joining multiple sources together, exactly the same conceptual operation as a SQL join or an Excel VLOOKUP/XLOOKUP

---

## 🔗 Related Notes

- [[Python Codex]]
- [[Python Cheat Sheet]]
- [[Excel Data Analyst Reference]]
- [[SQL Data Analyst Reference]]
- [[Power BI Data Analyst Reference]]
