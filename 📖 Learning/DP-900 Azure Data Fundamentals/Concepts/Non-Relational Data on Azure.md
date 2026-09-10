---
type: concept
status: published
created: 2026-09-10
updated: 2026-09-10
technology: Azure
difficulty: Beginner
tags:
  - dp-900
  - nosql
  - cosmos-db
  - azure-storage
aliases:
  - NoSQL on Azure
  - Cosmos DB
publish: true
permalink: dp-900-azure-data-fundamentals/non-relational-data-on-azure
---

# <span class="rune">ᛟ</span> Non-Relational Data on Azure

> *Four NoSQL shapes, one multi-model database that speaks all of them, and a storage account with four different personalities.*

---

## 🎯 Purpose

~15–20% of the exam — the smallest domain, but dense with specific facts (consistency levels, storage tiers, redundancy options) that show up as precise recall questions rather than reasoning questions.

---

## 🧠 Key Ideas

- Four NoSQL data models: key-value, document, column-family, graph
- Azure Cosmos DB is one engine exposed through multiple APIs, one per data model
- Azure Storage has four distinct services (Blob, Table, File, Queue) — don't confuse Table storage (NoSQL) with SQL tables
- Blob access tiers trade retrieval cost against storage cost
- Data Lake Storage Gen2 = Blob storage + hierarchical namespace

---

## ⚙️ How It Works

### NoSQL data models

| Model | Structure | Example use case | Azure service |
|---|---|---|---|
| **Key-value** | Simple key → value, no schema on the value | Session state, shopping carts | Cosmos DB (Table API), Azure Cache for Redis |
| **Document** | JSON-like documents, flexible fields | Product catalogs, user profiles | Cosmos DB (Core/SQL API) |
| **Column-family** | Rows with dynamic, grouped columns | Large-scale time-series, IoT telemetry | Cosmos DB (Cassandra API), Table storage |
| **Graph** | Nodes + edges = entities + relationships | Social networks, fraud detection | Cosmos DB (Gremlin API) |

### Azure Cosmos DB

- Globally distributed, multi-model, PaaS, single-digit-millisecond latency
- One engine, **multiple APIs**: Core (SQL/document), MongoDB, Cassandra, Gremlin (graph), Table
- **Automatic horizontal partitioning** — you choose a partition key
- **Global distribution** — replicate to any Azure region, optional multi-region writes
- **Five consistency levels**, strongest to weakest:

| Level | Trade-off |
|---|---|
| Strong | Highest consistency, highest latency |
| Bounded staleness | Consistency lag bounded by time/versions |
| **Session** (default, most-cited answer) | Best balance of consistency and performance |
| Consistent prefix | Reads never see out-of-order writes |
| Eventual | Lowest latency, weakest consistency |

### Azure Storage services

| Service | Type | Use case |
|---|---|---|
| **Blob** | Object storage (unstructured) | Images, video, backups, static site hosting |
| **Table** | Key-value / NoSQL | Simple structured data, no joins, cheap & fast |
| **File** | Managed file shares (SMB/NFS) | Lift-and-shift apps needing a shared drive |
| **Queue** | Message queuing | Decoupling app components, async processing |

### Blob access tiers

| Tier | Use case | Storage cost | Retrieval cost |
|---|---|---|---|
| Hot | Frequently accessed | Highest | Low |
| Cool | Infrequent, ≥30 days | Lower | Higher |
| Cold | Rare, ≥90 days | Lower still | Higher still |
| Archive | Rare, ≥180 days, offline | Lowest | Highest (hours to rehydrate) |

### Blob redundancy options

- **LRS** — 3 copies, one datacenter
- **ZRS** — copies across availability zones, one region
- **GRS** — LRS + async copy to a secondary region
- **RA-GRS** — GRS + read access to the secondary
- **GZRS / RA-GZRS** — ZRS + geo-replication (highest durability)

### Data Lake Storage Gen2

Blob storage **+ hierarchical namespace** (real folders/directories, not flat key naming). Built as the storage layer under Synapse, Databricks, and HDInsight — combines Blob's cost/scale with filesystem semantics (fast directory ops, POSIX ACLs) that big data engines need.

---

## 💻 Examples

```text
"Model a social network's friend-of-a-friend connections."
→ Cosmos DB, Gremlin (graph) API

"Data accessed once a year for compliance, cost is the top priority."
→ Blob storage, Archive tier

"Need read access to a geo-replicated copy in a secondary region."
→ RA-GRS (or RA-GZRS)
```

---

## 🚨 Common Mistakes

- Assuming "Table storage" means SQL tables — it's a NoSQL key-value store
- Picking Cool tier for data that's never accessed — Archive is cheaper if retrieval latency (hours) is acceptable
- Forgetting Data Lake Storage Gen2's defining feature is the hierarchical namespace, not just "it holds big data"

---

## 📖 Further Reading

- [Microsoft Learn: Explore non-relational data in Azure](https://learn.microsoft.com/en-us/training/paths/explore-non-relational-data-stores-basics/)

---

## 🔗 Related Notes

- [[Core Data Concepts]]
- [[Choosing the Right Cosmos DB API]] — drill note
- [[Picking a Blob Storage Access Tier]] — drill note
- [[DP-900 Codex]]
