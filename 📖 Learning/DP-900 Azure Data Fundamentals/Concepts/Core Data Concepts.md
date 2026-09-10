---
type: concept
status: active
created: 2026-09-10
updated: 2026-09-10
technology: Azure
difficulty: Beginner
tags:
  - dp-900
  - data-concepts
  - oltp
  - olap
aliases:
  - OLTP vs OLAP
  - Data Workloads
---

# 📚 Core Data Concepts

> *The vocabulary layer everything else in DP-900 is built on: what kind of data is this, what's being done with it, and whose job is that?*

---

## 🎯 Purpose

Before you can pick the right Azure service, you need to classify the data and the workload correctly. This domain is ~25–30% of the exam and almost entirely definitional — it's the "speak the language" test.

---

## 🧠 Key Ideas

- Data comes in three shapes: **structured**, **semi-structured**, **unstructured**
- Workloads split into two camps: **transactional (OLTP)** and **analytical (OLAP)**
- Four data professional roles show up repeatedly in scenario questions: DBA, data engineer, data analyst, data scientist
- ETL vs ELT is about *when* transformation happens relative to loading

---

## ⚙️ How It Works

### Types of data

| Type | Description | Examples | Typical store |
|---|---|---|---|
| **Structured** | Fixed schema, rows/columns, strict types | SQL tables, spreadsheets | Relational DB (Azure SQL) |
| **Semi-structured** | Self-describing, no fixed schema | JSON, XML, key-value pairs | NoSQL (Cosmos DB, Table storage) |
| **Unstructured** | No predefined model | Images, video, audio, free text | Blob storage, Data Lake |

Semi-structured data *does* have some internal structure (tags/keys) — that's what distinguishes it from unstructured. Don't conflate the two.

### OLTP vs OLAP

| | OLTP (Transactional) | OLAP (Analytical) |
|---|---|---|
| Purpose | Run the business | Analyze the business |
| Operations | Insert/Update/Delete — fast, small | Read/aggregate — complex, large |
| Design | Normalized | Denormalized (star/snowflake) |
| Users | Front-line apps, customers | Analysts, executives |
| Azure examples | Azure SQL Database, Cosmos DB | Azure Synapse Analytics, Power BI |

### Data professional roles

| Role | Focus | Verb to listen for |
|---|---|---|
| **DBA** | Provision, secure, monitor, maintain databases | secure / maintain / back up |
| **Data Engineer** | Build & manage pipelines that move/transform data | build / move / pipeline |
| **Data Analyst** | Explore data, produce reports & dashboards | visualize / report |
| **Data Scientist** | Build statistical/ML models to predict outcomes | predict / model |

### ETL vs ELT

- **ETL** (Extract, Transform, Load) — transform *before* loading into the target
- **ELT** (Extract, Load, Transform) — load raw data first, transform *inside* the target (common with data lakes + cheap compute)

### Batch vs streaming

- **Batch**: scheduled, processes data in chunks (e.g. nightly job)
- **Streaming**: continuous, near real-time, as data arrives

---

## 💻 Examples

```text
"A system processes thousands of small banking transactions per second."
→ OLTP

"A quarterly sales trend report aggregates two years of order history."
→ OLAP

"Someone designs a pipeline moving data from an on-prem SQL Server to a data lake."
→ Data Engineer

"Someone sets the backup retention policy and monitors failover."
→ DBA
```

---

## 🚨 Common Mistakes

- Calling JSON "unstructured" — it's semi-structured
- Assuming OLAP databases are normalized like OLTP — they're intentionally denormalized (star schema) for read speed
- Mixing up data engineer (builds pipelines) with data analyst (builds reports) in scenario questions

---

## 📖 Further Reading

- [Microsoft Learn: Explore core data concepts](https://learn.microsoft.com/en-us/training/paths/azure-data-fundamentals-explore-core-data-concepts/)

---

## 🔗 Related Notes

- [[Relational Data on Azure]] — where OLTP concepts get implemented in Azure
- [[Analytics Workloads on Azure]] — where OLAP concepts get implemented in Azure
- [[Spotting OLTP vs OLAP in a Scenario]] — drill note for this exact distinction
- [[DP-900 Codex]]
