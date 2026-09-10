# ⚡ Power BI Cheat Sheet

> Dense reference across Power BI, DAX, and Power Query. Each section links back to the full [[Power BI Codex|concept note]] for depth.

---

## Building Blocks

| Object | Lives in | Definition |
|---|---|---|
| Dataset (semantic model) | Desktop → Service | data + relationships + measures |
| Report | Desktop → Service | pages of visuals built from a dataset |
| Dashboard | Service only | tiles pinned from one or more reports |
| Workspace | Service | container for related datasets/reports/dashboards |
| App | Service | curated, published bundle for an audience |

See: [[Power BI Building Blocks]]

## Desktop / Service / Mobile

| App | Role |
|---|---|
| Desktop | author — data model, DAX, report pages |
| Service | publish, share, refresh, collaborate, dashboards |
| Mobile | view + light interaction |
| Report Builder | paginated (pixel-perfect) reports |

See: [[Power BI Desktop vs Service vs Mobile]]

---

## Data Modeling

- **Star schema**: one fact table (numeric measures + FKs) + dimension tables (descriptive attributes), each dimension one hop from the fact table
- **Snowflake schema**: dimensions normalized further — generally avoid in Power BI
- **Cardinality**: one-to-many (most common) · one-to-one · many-to-many
- **Cross-filter direction**: single (one way) vs. bidirectional (both ways — use sparingly)
- Only one relationship between two tables can be **active** at a time; use `USERELATIONSHIP()` to activate an inactive one for a specific calculation

See: [[Data Modeling and Relationships]] · [[Star Schema in Power BI]]

---

## Calculated Column vs Measure

| | Calculated Column | Measure |
|---|---|---|
| Computed | once, at refresh | on demand, per context |
| Stored | yes (adds to model size) | no |
| Responds to filters | no — frozen at refresh | yes — always |
| Usable as slicer/axis | yes | no |

See: [[Calculated Columns vs Measures]]

---

## DAX Context

| Context | Exists in | Knows |
|---|---|---|
| Row context | calculated columns, iterators (`SUMX`, `FILTER`) | the current row |
| Filter context | measures, always | whatever's currently filtered |

`CALCULATE()` triggers **context transition** — converts row context into filter context.

See: [[Row Context vs Filter Context]] · [[DAX Fundamentals]]

---

## CALCULATE() — the core function

```dax
CALCULATE(expression, filter1, filter2, ...)
```

- Filter arguments **replace** any existing filter on the same column (not add to it)
- `ALL(table_or_column)` — removes filters entirely
- `ALLSELECTED(table)` — respects slicers, ignores only the visual's internal row/column breakdown
- `KEEPFILTERS(condition)` — adds to, rather than replaces, an existing filter

See: [[The CALCULATE Function]] · [[Filtering a Measure with CALCULATE and FILTER]]

---

## Time Intelligence

| Function | Purpose |
|---|---|
| `DATESYTD(date_col)` | dates from Jan 1 to latest filtered date |
| `DATESQTD` / `DATESMTD` | same, quarter/month |
| `SAMEPERIODLASTYEAR(date_col)` | same dates, one year back |
| `DATEADD(date_col, n, unit)` | shift by day/month/quarter/year |
| `TOTALYTD(expr, date_col)` | shorthand YTD wrapper |

**Requires a dedicated, continuous date table, marked as the official Date table.**

See: [[Time Intelligence in DAX]] · [[Building a Date Table]] · [[Calculating Year-to-Date Totals]] · [[Calculating Year-over-Year Growth]]

---

## Common DAX Patterns

```dax
// Basic measure
Total Sales = SUM(Sales[SalesAmount])

// Safe ratio — never raw division
Margin % = DIVIDE([Total Profit], [Total Sales])

// Percent of total
% of Total = DIVIDE([Total Sales], CALCULATE([Total Sales], ALL(Sales)))

// Ranking
Product Rank = RANKX(ALL(Products[ProductName]), [Total Sales])

// Running total
Running Total = CALCULATE([Total Sales], FILTER(ALL(DimDate[Date]), DimDate[Date] <= MAX(DimDate[Date])))

// Conditional bucketing
Sales Tier =
SWITCH(TRUE(),
	[Total Sales] >= 10000, "Platinum",
	[Total Sales] >= 5000, "Gold",
	"Bronze"
)

// Variables for readable multi-step logic
Measure =
VAR CurrentVal = [Total Sales]
VAR PriorVal = CALCULATE([Total Sales], SAMEPERIODLASTYEAR(DimDate[Date]))
RETURN DIVIDE(CurrentVal - PriorVal, PriorVal)

// Pulling a value across a relationship (calculated column)
ProductCategory = RELATED(DimProduct[Category])
```

See: [[Creating a Basic Measure with SUM]] · [[Creating a Percentage of Total Measure]] · [[Ranking Values with RANKX]] · [[Creating a Running Total]] · [[Using SWITCH for Multiple Conditions]] · [[Simplifying DAX with Variables]] · [[Using RELATED Across a Relationship]]

---

## NULL / Blank Handling

```dax
DIVIDE(numerator, denominator)          -- returns BLANK() instead of erroring
DIVIDE(numerator, denominator, 0)       -- with a fallback value
ISBLANK([Measure])                      -- test for blank
COALESCE([Measure], 0)                  -- replace blank with a fallback
```

See: [[Handling Blank Values with DAX]] · [[NULL and Three-Valued Logic]]

---

## Power Query (M)

| Task | Where |
|---|---|
| Combine columns from two queries (join) | `Merge Queries` |
| Stack rows from two queries with matching structure | `Append Queries` |
| Bucket/label rows by a condition | `Add Column → Conditional Column` |
| Custom row-level logic | `Add Column → Custom Column` |

```m
let
	Source = ...,
	Filtered = Table.SelectRows(Source, each [Amount] > 0),
	Renamed = Table.RenameColumns(Filtered, {{"Amt", "Amount"}})
in
	Renamed
```

**Query folding**: transformations pushed down to the source (fast) vs. pulled and run locally in Power BI (slow). Put filters early; check "View Native Query" to confirm folding is still active.

See: [[Power Query (M) Basics]] · [[Query Folding]] · [[Merging Queries in Power Query]] · [[Adding a Conditional Column in Power Query]]

---

## Storage Modes

| Mode | Speed | Freshness | DAX support |
|---|---|---|---|
| Import | fastest | as of last refresh | full |
| DirectQuery | source-dependent | always current | some restrictions |
| Live Connection | source-dependent | always current | consumes external model, no local editing |

Default to Import unless there's a specific reason not to.

See: [[Storage Modes - Import vs DirectQuery vs Live Connection]]

---

## Architecture

| Term | Purpose |
|---|---|
| Power BI Gateway | bridges cloud Service to on-prem data sources |
| Dataflow | shared, reusable Power Query layer hosted in the Service |
| Row-Level Security (RLS) | restricts which rows a user sees, via a DAX filter per role |

RLS types: **static** (fixed value per role) vs **dynamic** (`USERPRINCIPALNAME()` looked up against a mapping table).

See: [[Power BI Gateway]] · [[Dataflows]] · [[Row-Level Security (RLS)]]

---

## Choosing a Visual

| Question | Visual |
|---|---|
| Trend over time | line chart |
| Compare categories | bar/column chart |
| Part-to-whole | stacked bar / treemap |
| Relationship between 2 numbers | scatter chart |
| One key number | card / KPI |
| Exact values, many dimensions | table / matrix |
| Geographic | map |

See: [[Choosing the Right Visual]] · [[Bookmarks and Drill-Through]]

---

## 🔗 Related Notes

- [[Power BI Codex]]
