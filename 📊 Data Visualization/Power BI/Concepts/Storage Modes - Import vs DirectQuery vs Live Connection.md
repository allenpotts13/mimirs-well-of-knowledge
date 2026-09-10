---
type: concept
status: published
created: 2026-09-10
updated: 2026-09-10
technology: Power BI
difficulty: Intermediate
tags:
  - power-bi
  - performance
  - architecture
aliases:
  - DirectQuery
  - Import Mode
  - Live Connection
publish: true
---

# <span class="rune">ᛟ</span> Storage Modes - Import vs DirectQuery vs Live Connection

> How a dataset actually gets its data determines everything about performance, freshness, and what DAX features are even available.

---

## 🎯 Purpose

Choosing a storage mode is one of the first, highest-impact decisions in building a Power BI model — it affects refresh strategy, query speed, data freshness, and licensing considerations.

---

## 🧠 Key Ideas

- **Import** — data is copied into Power BI's own compressed in-memory engine (VertiPaq) on refresh. Fastest queries, but data is only as fresh as the last refresh.
- **DirectQuery** — no data is copied; every visual sends a live query back to the source system. Always current, but query speed depends entirely on the source database's performance.
- **Live Connection** — similar to DirectQuery but specifically for connecting to an existing Analysis Services or Power BI dataset, reusing its model rather than the source database directly.
- **Composite models** mix storage modes across different tables in the same model (some Import, some DirectQuery).
- Import mode supports the full DAX feature set; DirectQuery has some restrictions and performance considerations that don't apply to Import.

---

## ⚙️ How It Works

```text
Import:            Refresh → copy + compress data into Power BI → query the local copy (fast)
DirectQuery:        Every visual interaction → live query sent to the source (as fast as the source)
Live Connection:     Every visual interaction → query sent to an existing external dataset/model
```

---

## 💻 Examples

```text
"Dashboard needs to reflect data updated every few minutes, and the
source database can handle the query load."
→ DirectQuery

"Dataset refreshes nightly, report just needs to be fast for a large
audience."
→ Import

"Multiple reports need to share one governed, centrally-maintained
data model hosted in the Service."
→ Live Connection to a shared dataset
```

---

## 🚀 Real World Applications

- Choosing between "always current, source-dependent speed" and "fast, but only as fresh as the last refresh"
- Reusing a single certified dataset across many reports via Live Connection, for governance and consistency
- Blending a small, frequently-changing table (DirectQuery) with a large, stable historical table (Import) in a composite model

---

## ⚖️ Advantages

**Import**: fastest visual interaction, full DAX support, works offline from the source after refresh.
**DirectQuery**: always reflects the latest source data, no duplicate storage.
**Live Connection**: centralizes the model in one place, enforced consistently across every connected report.

---

## ⚠️ Limitations

**Import**: data is stale between refreshes; large datasets increase refresh time and consume capacity/storage.
**DirectQuery**: query performance is capped by the source system; some DAX functions are restricted or behave differently.
**Live Connection**: report authors can't modify the underlying data model — only build visuals against it.

---

## 🚨 Common Mistakes

- Choosing DirectQuery for "real-time" requirements without checking whether the source database can actually handle the resulting query load from many concurrent users.
- Assuming DirectQuery supports every DAX pattern Import does — some time intelligence and complex measures behave differently or are restricted.
- Building a composite model without understanding which tables are Import vs. DirectQuery, causing confusing performance inconsistencies.

---

## 📖 Further Reading

Documentation

Books

Videos

Articles

---

## 💡 Wisdom from Mímir

Default to Import unless there's a specific, named reason not to — near-real-time freshness or a dataset too large to practically import. DirectQuery trades away a lot of speed and DAX flexibility for freshness that most reports don't actually need minute-to-minute.

---

## 🔗 Related Notes

- [[Query Folding]]
- [[Power BI Gateway]]
- [[Dataflows]]
