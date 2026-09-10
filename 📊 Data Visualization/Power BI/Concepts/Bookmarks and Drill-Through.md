---
type: concept
status: published
created: 2026-09-10
updated: 2026-09-10
technology: Power BI
difficulty: Intermediate
tags:
  - power-bi
  - visualization
  - interactivity
aliases:
  - Power BI Bookmarks
  - Drill-Through
  - Drillthrough
publish: true
---

# 📚 Bookmarks and Drill-Through

> Two different tools for report navigation — one snapshots a view's current state, the other jumps to a filtered detail page based on what was clicked.

---

## 🎯 Purpose

Both let a report do more than sit static — bookmarks build guided narratives or toggleable views, drill-through builds a "click a category, see its details" navigation flow, without needing a separate report per scenario.

---

## 🧠 Key Ideas

- **Bookmark** — captures a snapshot of the current report state: filters, slicer selections, visibility of objects, even the current page. Can be replayed with a click.
- Bookmarks power **buttons that toggle views** (e.g. switching between a chart and a table showing the same data) and guided, presentation-style walkthroughs.
- **Drill-through** — a separate report page configured to receive a filter when a user right-clicks (or clicks a drill-through button on) a data point on another page.
- Drill-through pages automatically filter to the clicked value — no manual slicer setup needed by the viewer.
- Both are built entirely in the UI — no DAX or Power Query required for either.

---

## ⚙️ How It Works

```text
Bookmark:       [current filters/slicers/visibility] → saved snapshot → replay on click

Drill-through:   click a data point (e.g. "West" region bar)
                       ↓
                 jump to a detail page, pre-filtered to Region = "West"
```

---

## 💻 Examples

```text
"Build a button that switches the same visual area between a chart
view and a table view."
→ Two bookmarks (one per state) + a button/toggle group

"Click a product category on the summary page and land on a detail
page already filtered to that category."
→ Drill-through page configured to filter on Category

"Walk an executive through the report in a specific, guided order."
→ A bookmark-based navigation sequence (a "story")
```

---

## 🚀 Real World Applications

- Executive presentation flows that guide a viewer through a fixed narrative
- Toggle buttons for switching between chart types or summary/detail views
- Category or product drill-through pages that avoid cluttering the summary page with every detail at once

---

## ⚖️ Advantages

- Adds real interactivity and navigation without writing any code.
- Drill-through keeps summary pages clean while still making detail just a click away.
- Bookmarks can combine filter state and object visibility, enabling flexible "choose your own view" reports.

---

## ⚠️ Limitations

- Bookmarks can be fragile — adding or renaming visuals after bookmarks are created can silently break saved states.
- Drill-through requires the target page to have a matching field to filter on — mismatched fields silently fail to filter correctly.
- Overusing bookmarks for complex show/hide logic can make a report hard to maintain.

---

## 🚨 Common Mistakes

- Renaming or deleting a visual referenced by an existing bookmark, breaking it without an obvious error message.
- Forgetting to add the filter field to the drill-through page's filter well, breaking the automatic filtering.
- Building an elaborate bookmark-based interaction when a simple slicer would serve the same purpose more transparently.

---

## 📖 Further Reading

Documentation

Books

Videos

Articles

---

## 💡 Wisdom from Mímir

Test every bookmark and drill-through path after any structural change to the report — a renamed field or a deleted visual is a silent, common way these interactions break without any error to flag it.

---

## 🔗 Related Notes

- [[Choosing the Right Visual]]
- [[Power BI Building Blocks]]
