---
type: concept
status: published
created: 2026-09-10
updated: 2026-09-10
technology: Power BI
difficulty: Intermediate
tags:
  - power-bi
  - etl
  - architecture
aliases:
  - Power BI Dataflow
publish: true
permalink: power-bi/dataflows
---

# <span class="rune">ᛟ</span> Dataflows

> Power Query transformations, moved out of individual reports and into a shared, reusable, cloud-hosted layer that many datasets can pull from.

---

## 🎯 Purpose

Without dataflows, the same Power Query cleanup logic tends to get copy-pasted into every report that needs the same source data — meaning the same bug gets copy-pasted too. Dataflows centralize that logic in one place, refreshed once, reused everywhere.

---

## 🧠 Key Ideas

- Built with the same Power Query (M) engine used in Desktop, but authored and hosted directly in the Power BI Service.
- Output is stored in Azure Data Lake Storage behind the scenes, as a set of reusable tables ("entities").
- Multiple datasets — even across different workspaces, depending on permissions — can connect to the same dataflow instead of rebuilding the same query logic.
- Refreshed independently of any specific dataset, on its own schedule.
- Different from a dataset: a dataflow only prepares/cleans data — it has no relationships, measures, or DAX. That layer still lives in the dataset that consumes it.

---

## ⚙️ How It Works

```text
Source systems
      ↓
Dataflow (Power Query transformations, hosted in the Service)
      ↓ (stored in Azure Data Lake Storage)
Dataset A ──┐
Dataset B ──┼── all reuse the same cleaned dataflow output
Dataset C ──┘
```

---

## 💻 Examples

```text
"Five different reports across three workspaces all need the same
cleaned, deduplicated customer table from the CRM."
→ Build one dataflow, have all five datasets connect to it

"A complex, slow-to-transform source table is reused in multiple places."
→ Dataflow centralizes and refreshes the transformation once,
   instead of every dataset paying that cost independently
```

---

## 🚀 Real World Applications

- Centralizing a common "cleaned customer table" or "standard date table" used across many reports
- Reducing duplicate transformation logic (and duplicate bugs) across a team's reports
- Separating data preparation (owned by a data team) from report/dashboard building (owned by analysts)

---

## ⚖️ Advantages

- Single source of truth for cleaned, transformed data — fix a bug once, every downstream dataset benefits.
- Refreshes independently, so heavy transformation work doesn't repeat across every dataset that needs it.
- Lets a data engineering team own the transformation layer while report authors focus on modeling and visuals.

---

## ⚠️ Limitations

- Adds an extra layer of architecture and permissions to manage.
- Refresh scheduling now has two layers to coordinate — the dataflow, and each dataset that depends on it.
- Not available on every Power BI license tier without the right capacity/premium features.

---

## 🚨 Common Mistakes

- Building the same transformation separately in multiple datasets instead of centralizing it in a dataflow once the pattern repeats.
- Forgetting a dataset depends on a dataflow's refresh completing first — refreshing the dataset before the dataflow updates serves stale data.
- Treating a dataflow as a place for modeling/DAX — that logic still belongs downstream, in the dataset.

---

## 📖 Further Reading

Documentation

Books

Videos

Articles

---

## 💡 Wisdom from Mímir

The moment the same Power Query steps get copy-pasted into a second report, that's the signal to promote them into a dataflow instead. Two copies of the same transformation logic is exactly how the same bug ends up needing to be fixed twice.

---

## 🔗 Related Notes

- [[Power Query (M) Basics]]
- [[Power BI Gateway]]
