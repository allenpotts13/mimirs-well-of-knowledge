---
type: concept
status: published
created: 2026-09-10
updated: 2026-09-10
technology: Power BI
difficulty: Beginner
tags:
  - power-bi
  - fundamentals
aliases:
  - Dataset Report Dashboard
  - Power BI Workspace
publish: true
---

# <span class="rune">ᛟ</span> Power BI Building Blocks

> Five nested objects — dataset, report, dashboard, workspace, app — that everything else in Power BI is assembled from.

---

## 🎯 Purpose

Every Power BI conversation eventually needs these five terms used precisely. Confusing a "report" with a "dashboard," or a "dataset" with a "workspace," is the single most common source of miscommunication with a new Power BI user.

---

## 🧠 Key Ideas

- **Dataset (semantic model)** — the data connection, tables, relationships, and measures a report is built from.
- **Report** — one or more pages of visuals, all built from a single dataset.
- **Dashboard** — a single canvas of "tiles" pinned from one or more reports. Service-only — there is no dashboard in Desktop.
- **Workspace** — a container in the Service for organizing and sharing related datasets, reports, and dashboards.
- **App** — a curated, read-only bundle of content from a workspace, published to a wider audience.

---

## ⚙️ How It Works

```text
Dataset (semantic model)
   └── Report (pages of visuals built from that dataset)
         └── Dashboard (tiles pinned from one or more reports)

Workspace (container for all of the above)
   └── App (packaged, published view of a workspace's content)
```

A dataset can power multiple reports. A dashboard can pull tiles from multiple reports, even across different datasets, as long as they're in the same workspace.

---

## 💻 Examples

```text
"Build a data model connecting to a sales database."
→ Dataset

"Build 3 pages showing sales by region, by product, and by rep."
→ Report

"Pin one visual from each of those 3 pages onto a single executive summary screen."
→ Dashboard

"Share the whole finished package with the sales leadership team, read-only."
→ Publish an App from the workspace
```

---

## 🚀 Real World Applications

- Structuring a Power BI project before building anything
- Explaining to a stakeholder why "just add this to the dashboard" isn't the same request as "add this to the report"
- Deciding what belongs in a shared workspace vs. an app for read-only distribution

---

## ⚖️ Advantages

- A clean separation between authoring (dataset/report) and consumption (dashboard/app).
- One dataset reused across many reports avoids rebuilding the data model repeatedly.

---

## ⚠️ Limitations

- Dashboards can only pin whole visuals — they can't be edited directly, only rebuilt in the source report.
- Apps are a snapshot of a workspace's content at publish/update time, not live-editable by consumers.

---

## 🚨 Common Mistakes

- Asking to "edit the dashboard" when the actual change belongs in the underlying report.
- Assuming a report and a dataset are the same thing — a report is just one possible view of a dataset.
- Building a separate dataset per report instead of reusing one shared, well-modeled dataset.

---

## 📖 Further Reading

Documentation

Books

Videos

Articles

---

## 💡 Wisdom from Mímir

When someone asks for "a change to the dashboard," the first question is always "which report is that tile pinned from?" The dashboard itself is just a frozen picture of a visual — the real editing always happens one layer down.

---

## 🔗 Related Notes

- [[Power BI Desktop vs Service vs Mobile]]
- [[Data Modeling and Relationships]]
