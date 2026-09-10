---
type: concept
status: active
created: 2026-09-10
updated: 2026-09-10
technology: Azure
difficulty: Beginner
tags:
  - dp-900
  - analytics
  - synapse
  - power-bi
  - etl
aliases:
  - Modern Data Warehouse
  - Power BI Basics
---

# 📚 Analytics Workloads on Azure

> *The pipeline that turns raw data into a Power BI dashboard, and every Azure service that lives along the way.*

---

## 🎯 Purpose

The biggest domain, ~25–30% of the exam. Covers the end-to-end modern data warehouse pattern, the big-data compute services, star schema design, and Power BI's building blocks.

---

## 🧠 Key Ideas

- One pipeline shape recurs everywhere: ingest → store → prep/train → model/serve → visualize
- Data Factory orchestrates/moves data; Stream Analytics processes it in real time — different jobs, commonly confused
- Synapse, Databricks, and HDInsight overlap but have a clear "best fit" scenario each
- Star schema = one fact table (numbers) + dimension tables (context)
- Power BI dashboards only exist in the **Service**, never in Desktop

---

## ⚙️ How It Works

### The modern data warehouse pattern (draw this from memory)

```
[Sources] → [Ingest] → [Store] → [Prep & Train] → [Model & Serve] → [Visualize]
 on-prem DBs  Data       Data      Synapse /         Analysis         Power BI
 SaaS apps    Factory    Lake      Databricks /       Services
 IoT/streams  Event Hubs Storage   HDInsight
```

### Ingestion & orchestration

| Service | Purpose |
|---|---|
| **Azure Data Factory** | Cloud ETL/ELT + orchestration — pipelines that move & transform data on a schedule/trigger, low/no-code |
| **Azure Synapse Pipelines** | Same engine as ADF, built into Synapse |
| **Azure Event Hubs** | Big-data streaming ingestion — millions of events/sec |
| **Azure IoT Hub** | Bi-directional hub specifically for IoT devices |
| **Azure Stream Analytics** | Real-time stream processing, SQL-like query language, sits over Event Hubs/IoT Hub/Blob |

> Rule of thumb: "move/transform data between stores on a schedule" → **Data Factory**. "Process data in real time as it streams in" → **Stream Analytics**.

### Batch vs streaming

| | Batch | Streaming (real-time) |
|---|---|---|
| Data | Large volumes, scheduled | Continuous, as it arrives |
| Latency | Minutes–hours | Milliseconds–seconds |
| Example | Nightly sales aggregation | Fraud detection, live dashboards |
| Services | Data Factory, Synapse, HDInsight | Stream Analytics, Event Hubs, Databricks streaming |

### Big data & analytics compute

| Service | What it is | Best for |
|---|---|---|
| **Azure Synapse Analytics** | Unified platform: SQL pools (warehousing) + Spark pools (big data) + pipelines + Power BI integration | End-to-end enterprise analytics, one workspace |
| **Azure Databricks** | Managed Apache Spark, collaborative notebooks | Data engineering + data science/ML at scale |
| **Azure HDInsight** | Managed open-source clusters (Hadoop, Spark, Hive, Kafka, HBase) | Migrating existing open-source big-data workloads |
| **Azure Analysis Services** | Managed tabular semantic modeling engine | Semantic models Power BI/Excel query against |

### Data warehousing concepts

- **Fact table** — numeric, additive measures + FKs to dimensions (e.g. sales amount)
- **Dimension table** — descriptive context: who/what/when/where (e.g. date, product, customer)
- **Star schema** — one fact table surrounded by dimension tables (the standard OLAP design)
- **Snowflake schema** — a star schema whose dimensions are further normalized into sub-dimensions
- **Data mart** — a subset of a data warehouse scoped to one business area

### Power BI

| App | Role |
|---|---|
| **Power BI Desktop** | Windows authoring tool — build reports & data models |
| **Power BI Service** | Cloud — publish, share, collaborate; **dashboards live only here** |
| **Power BI Mobile** | View reports/dashboards on phone/tablet |
| **Power BI Report Builder** | Pixel-perfect, printable "paginated reports" |

| Building block | Definition |
|---|---|
| **Dataset** | The data model/connection powering reports |
| **Report** | Pages of visualizations built from a dataset |
| **Dashboard** | A single canvas of pinned tiles from one or more reports — Service only |
| **Workspace** | Container for organizing/sharing related content |
| **App** | A packaged, curated bundle distributed to an audience |

Visual selection: **trend over time** → line chart · **comparison** → bar/column · **part-to-whole** → pie/stacked bar · **relationship** → scatter · **single KPI** → card/gauge.

---

## 💻 Examples

```text
"Low-code pipeline copies data nightly from on-prem SQL Server into a data lake."
→ Azure Data Factory

"Process IoT telemetry in real time with a SQL-like query language."
→ Azure Stream Analytics

"One workspace bundling data warehousing, Spark, and pipelines."
→ Azure Synapse Analytics

"Can I build a dashboard in Power BI Desktop?"
→ No — dashboards exist only in the Power BI Service
```

---

## 🚨 Common Mistakes

- Swapping Data Factory (orchestration/movement) and Stream Analytics (real-time processing) in scenario answers
- Calling a fact table "descriptive" — facts are the numbers; dimensions are the descriptions
- Assuming Desktop and Service are interchangeable — dashboards are Service-only

---

## 📖 Further Reading

- [Microsoft Learn: Explore data analytics in Azure](https://learn.microsoft.com/en-us/training/paths/azure-data-fundamentals-explore-data-warehouse-analytics/)

---

## 🔗 Related Notes

- [[Core Data Concepts]]
- [[Data Factory vs Stream Analytics vs Synapse]] — drill note
- [[Star Schema - Fact Tables vs Dimension Tables]] — drill note
- [[Power BI Desktop vs Service vs Mobile (DP-900)]] — drill note
- [[Power BI Codex]] — full practitioner-level Power BI reference (data modeling, DAX, Power Query)
- [[DP-900 Codex]]
