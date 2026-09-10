---
type: concept
status: active
created: 2026-09-10
updated: 2026-09-10
technology: Power BI
difficulty: Beginner
tags:
  - power-bi
  - visualization
  - design
aliases:
  - Power BI Visual Types
  - Chart Selection
---

# 📚 Choosing the Right Visual

> The question being asked determines the chart — not the other way around. Pick the shape that matches what the data needs to communicate.

---

## 🎯 Purpose

The most common report design mistake isn't a wrong number — it's the right number shown in the wrong shape, hiding an insight the audience needed to see immediately.

---

## 🧠 Key Ideas

- **Trend over time** → line chart
- **Comparison across categories** → bar/column chart
- **Part-to-whole** → stacked bar, treemap, or (sparingly) pie/donut chart
- **Relationship between two numeric variables** → scatter chart
- **Single important number** → card or KPI visual
- **Precise values across many dimensions** → table or matrix
- **Geographic data** → map or filled map visual
- Every visual should answer a specific question — if it's not obvious what question a visual answers, it probably needs rethinking.

---

## ⚙️ How It Works

Match the *shape of the comparison* being made to the chart type built for that shape:

```text
Change over time?          → line chart
Compare categories?         → bar/column chart
Show parts of a whole?      → stacked bar / treemap
Show a relationship?        → scatter chart
Show one key number?        → card / KPI
Need exact values, many
dimensions at once?         → table / matrix
```

---

## 💻 Examples

```text
"Show monthly revenue for the past 2 years."
→ Line chart

"Compare total sales across 5 product categories."
→ Bar chart

"Show this quarter's total revenue as a single headline number."
→ Card visual

"Show revenue by both region and product simultaneously, with exact
values."
→ Matrix visual
```

---

## 🚀 Real World Applications

- Building an executive summary dashboard where a viewer needs the key insight in under 5 seconds
- Choosing between a table and a chart when precision vs. pattern-recognition matters more
- Avoiding pie charts with more than 3-4 slices, where a bar chart reads faster and more accurately

---

## ⚖️ Advantages

- A well-matched visual communicates instantly, without requiring the viewer to study it.
- Consistent visual choices across a report build a "visual vocabulary" that makes the whole report faster to read.

---

## ⚠️ Limitations

- No visual works for every audience — a table might suit an analyst but overwhelm an executive expecting a single number.
- Some genuinely ambiguous cases (ranked categories with a time element) don't have one obviously "correct" visual.

---

## 🚨 Common Mistakes

- Defaulting to a pie chart for more than a handful of categories — differences in slice angle are hard to compare accurately past 3-4 slices.
- Using a 3D or heavily styled visual that prioritizes decoration over readability.
- Cramming too many data points onto one visual instead of splitting into multiple focused visuals or using a slicer.
- Choosing a chart type based on what looks impressive rather than what answers the actual business question.

---

## 📖 Further Reading

Documentation

Books

Videos

Articles

---

## 💡 Wisdom from Mímir

Before building any visual, finish this sentence out loud: "this visual answers the question ___." If the sentence doesn't finish cleanly, the visual (or the data behind it) isn't ready yet.

---

## 🔗 Related Notes

- [[Power BI Building Blocks]]
- [[Bookmarks and Drill-Through]]
