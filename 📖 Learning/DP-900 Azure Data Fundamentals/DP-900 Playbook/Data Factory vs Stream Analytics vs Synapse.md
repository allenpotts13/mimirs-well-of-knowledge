---
type: exam-scenario
status: published
created: 2026-09-10
updated: 2026-09-10
technology: Azure
difficulty: Beginner
tags:
  - dp-900
  - analytics
  - synapse
  - data-factory
aliases:
  - Analytics Service Overlap
publish: true
permalink: dp-900-azure-data-fundamentals/data-factory-vs-stream-analytics-vs-synapse
---

# <span class="rune">ᚲ</span> Data Factory vs Stream Analytics vs Synapse

> *Three services that all touch "analytics" but each own a distinct piece of the pipeline.*

---

## 🎯 Problem

These three names get scrambled together because they all sound like "the Azure analytics thing." The exam expects you to know exactly which stage of the pipeline each one owns.

---

## 🤔 Mental Model

```
[Sources] → [Data Factory: ingest/orchestrate] → [Data Lake: store]
                                                        ↓
                              [Synapse: warehouse + Spark + pipelines, all-in-one]
                                                        ↓
                              [Stream Analytics: real-time query layer, if streaming]
                                                        ↓
                                              [Power BI: visualize]
```

---

## 🧠 Why This Pattern Works

- **Data Factory** = the mover. Orchestrates ETL/ELT pipelines on a schedule or trigger. No analytics compute of its own — it calls out to other services to do transformation work.
- **Stream Analytics** = the real-time query engine. Written in a SQL-like language, sits between an event source (Event Hubs/IoT Hub) and a sink (Blob, Power BI, database).
- **Synapse Analytics** = the platform. Bundles SQL pools (data warehousing), Spark pools (big data processing), and its own built-in pipelines (same engine as Data Factory) into one workspace. It doesn't replace Data Factory or Stream Analytics conceptually — it can absorb Data Factory-style pipelines into itself, but Stream Analytics remains a separate service for real-time streaming.

---

## 🔑 Decision Table

| Scenario signal | Service |
|---|---|
| "Low/no-code pipeline moves data from Source A to Source B on a schedule" | Data Factory |
| "One unified workspace with data warehousing and big data processing together" | Synapse Analytics |
| "Query a live event stream in near real-time" | Stream Analytics |
| "Need Spark notebooks for data engineering" | Synapse (Spark pools) or Databricks |

---

## 🚨 Common Mistakes

- Treating Synapse as "just a bigger Data Factory" — Synapse's differentiator is bundling warehousing + big data compute + pipelines, not merely more orchestration
- Picking Stream Analytics for a question that describes scheduled/batch movement — that's a Data Factory scenario even if the word "analytics" appears in the sentence

---

## 💡 Wisdom from Mímir

When multiple services could technically apply, the exam usually wants the *most specific fit named in the scenario*, not the most powerful/general one. If the scenario only needs simple scheduled movement, the answer is Data Factory even though Synapse could also do it.

---

## 🔗 Related Notes

- [[Analytics Workloads on Azure]] — full concept writeup
- [[Batch vs Streaming - Which Azure Service Fits]]
- [[DP-900 Codex]]
