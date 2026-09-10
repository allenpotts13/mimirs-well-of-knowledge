---
type: cheat-sheet
status: published
created: 2026-09-10
updated: 2026-09-10
technology: Azure
tags:
  - dp-900
  - cheat-sheet
aliases:
  - DP-900 Quick Reference
publish: true
---

# ⚡ DP-900 Cheat Sheet

> *Last-mile reference. If you can fill in every row of this from memory, you're ready.*

---

## Data types

| Type | Example |
|---|---|
| Structured | SQL tables |
| Semi-structured | JSON, XML |
| Unstructured | Images, video, PDFs |

## OLTP vs OLAP

| | OLTP | OLAP |
|---|---|---|
| Purpose | Run the business | Analyze the business |
| Schema | Normalized | Denormalized (star) |
| Azure | SQL Database, Cosmos DB | Synapse Analytics |

## Data roles

| Role | Verb |
|---|---|
| DBA | secure, maintain, back up |
| Data Engineer | build, move, pipeline |
| Data Analyst | visualize, report |
| Data Scientist | predict, model |

## Azure SQL family

| Service | Model | Fit |
|---|---|---|
| SQL Server on Azure VM | IaaS | Full control |
| Azure SQL Managed Instance | PaaS | Lift-and-shift, SQL Agent, cross-DB queries |
| Azure SQL Database | PaaS | Cloud-native, minimal admin |

- **DTU** = bundled resources, fixed tiers
- **vCore** = independent compute/storage, supports Hybrid Benefit
- **Serverless** = auto-pause, per-second billing
- **Elastic pool** = shared resources, many small DBs
- **TDE** = encrypts at rest (whole DB, automatic)
- **Always Encrypted** = client-side, column-level
- **Dynamic Data Masking** = obscures results for non-privileged users

## NoSQL models → Cosmos DB API

| Model | API |
|---|---|
| Document | Core (SQL) API / MongoDB API |
| Key-value | Table API |
| Column-family | Cassandra API |
| Graph | Gremlin API |

**Cosmos DB consistency levels** (strong → weak): Strong · Bounded Staleness · **Session** (default/most-cited) · Consistent Prefix · Eventual

## Azure Storage services

| Service | Type |
|---|---|
| Blob | Object/unstructured |
| Table | NoSQL key-value |
| File | SMB/NFS shares |
| Queue | Messaging |

**Blob tiers**: Hot (frequent) → Cool (≥30d) → Cold (≥90d) → Archive (≥180d, offline, hours to rehydrate)

**Redundancy**: LRS (1 region) → ZRS (zones) → GRS (+ secondary region) → RA-GRS (+ read access) → GZRS/RA-GZRS (zones + geo)

**Data Lake Storage Gen2** = Blob + hierarchical namespace

## Analytics pipeline

```
Sources → Ingest (Data Factory / Event Hubs / IoT Hub) → Store (Data Lake)
        → Prep & Train (Synapse / Databricks / HDInsight)
        → Model & Serve (Analysis Services) → Visualize (Power BI)
```

| Service | Job |
|---|---|
| Data Factory | Orchestrate/move data, batch ETL/ELT |
| Event Hubs | Ingest high-volume streaming events |
| IoT Hub | Bi-directional IoT device comms |
| Stream Analytics | Real-time query/processing, SQL-like syntax |
| Synapse Analytics | Unified: SQL pools + Spark pools + pipelines |
| Databricks | Managed Spark, notebooks, data engineering/ML |
| HDInsight | Managed open-source big data clusters |

## Star schema

- **Fact table** = numbers + FKs (what you `SUM()`)
- **Dimension table** = descriptive attributes (what you `GROUP BY`)
- **Snowflake schema** = dimensions normalized further

## Power BI

| App | Role | Dashboards? |
|---|---|---|
| Desktop | Author reports/models | ❌ |
| Service | Publish/share/collaborate | ✅ only here |
| Mobile | View | ❌ |
| Report Builder | Paginated (pixel-perfect) reports | ❌ |

**Dataset** → **Report** (pages of visuals) → **Dashboard** (pinned tiles, Service only) → **Workspace** (container) → **App** (curated bundle)

---

## 🔗 Related Notes

- [[DP-900 Codex]]
