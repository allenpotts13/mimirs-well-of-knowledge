---
type: concept
status: published
created: 2026-09-10
updated: 2026-09-10
technology: Azure
difficulty: Beginner
tags:
  - dp-900
  - relational
  - sql
  - azure-sql
aliases:
  - Azure SQL Family
publish: true
---

# 📚 Relational Data on Azure

> *Relational theory you already know from [[SQL Codex]], mapped onto which Azure product to actually deploy.*

---

## 🎯 Purpose

~20–25% of the exam. Half is relational database theory (tables, keys, normalization — light review), half is knowing which Azure relational service fits which scenario. The scenario-matching is where most points are won or lost.

---

## 🧠 Key Ideas

- Three deployment options for SQL Server-compatible workloads sit on an IaaS → PaaS spectrum
- DTU vs vCore are two different ways to *pay* for Azure SQL Database compute
- Azure SQL security is layered: encryption at rest (TDE), encryption in transit, column-level (Always Encrypted), row-level masking (Dynamic Data Masking)
- HA/DR is largely automatic in the PaaS tiers, manual in the IaaS tier

---

## ⚙️ How It Works

### Relational fundamentals (quick review)

- **Primary key (PK)** — uniquely identifies a row
- **Foreign key (FK)** — references another table's PK, forming a relationship
- **Normalization** — reduces redundancy (1NF → 2NF → 3NF); see [[Indexes]] and your deeper [[SQL Codex|SQL notes]] for the full treatment
- Relationship types: one-to-one, one-to-many, many-to-many (needs a junction table)

### The Azure SQL family — IaaS → PaaS spectrum

```
SQL Server on Azure VM   →   Azure SQL Managed Instance   →   Azure SQL Database
  (most control,               (near-100% SQL Server            (least admin,
   most admin work)              compatibility, still PaaS)       most managed)
```

| Service | What it is | Best for |
|---|---|---|
| **Azure SQL Database** | Fully managed PaaS (single DB or elastic pool) | New cloud-native apps, minimal admin overhead |
| **Azure SQL Managed Instance** | PaaS, near-100% SQL Server engine compatibility | Lift-and-shift with minimal code changes, needs SQL Agent / cross-DB queries |
| **SQL Server on Azure VM** | IaaS — full SQL Server on a VM you manage | Need full OS/DB-level control or a specific legacy version |
| **Azure Database for MySQL / PostgreSQL / MariaDB** | Managed PaaS for the respective open-source engine | Existing apps already built on that engine |

### Purchasing / deployment models

- **DTU (Database Transaction Unit)** — bundled CPU + memory + I/O, simple fixed tiers (Basic/Standard/Premium)
- **vCore** — compute and storage chosen independently, more control, supports Azure Hybrid Benefit (bring your own SQL Server license)
- **Serverless** compute tier — auto-scales and auto-pauses during inactivity, billed per second — good for intermittent, unpredictable workloads
- **Elastic pool** — shares resources across many databases with variable usage patterns — cost-efficient for lots of small DBs

### High availability, backup & security

- Azure SQL Database/MI include **automated backups** and **built-in HA** out of the box
- **Auto-failover groups / geo-replication** — replicate to a secondary region for DR
- **Point-in-time restore** — roll back to any point within the retention window
- **Transparent Data Encryption (TDE)** — encrypts data at rest, on by default
- **Always Encrypted** — client-side encryption of specific sensitive columns
- **Dynamic Data Masking** — obscures sensitive data in query results for non-privileged users, without changing the stored data

---

## 💻 Examples

```text
"Migrating an on-prem SQL Server app that relies on SQL Agent jobs and
cross-database queries, minimal code changes wanted."
→ Azure SQL Managed Instance

"A dev team wants a database that auto-pauses and bills per-second when idle."
→ Azure SQL Database, Serverless compute tier, vCore model

"A finance team needs credit card numbers unreadable to support staff
querying the table, without changing the stored data."
→ Dynamic Data Masking
```

---

## 🚨 Common Mistakes

- Picking Azure SQL Database for a lift-and-shift that needs cross-database queries or SQL Agent — that's a Managed Instance signal
- Confusing Always Encrypted (protects specific columns, client-side) with TDE (encrypts the whole database at rest, transparent to the app)
- Assuming DTU and vCore are interchangeable — vCore is required for Azure Hybrid Benefit and independent compute/storage scaling

---

## 📖 Further Reading

- [Microsoft Learn: Explore relational data in Azure](https://learn.microsoft.com/en-us/training/paths/azure-data-fundamentals-explore-relational-data/)

---

## 🔗 Related Notes

- [[Core Data Concepts]]
- [[Choosing Between Azure SQL Database, Managed Instance, and SQL Server on a VM]] — drill note for this exact decision
- [[SQL Codex]] — deeper query-level knowledge
- [[DP-900 Codex]]
