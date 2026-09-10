---
type: exam-scenario
status: active
created: 2026-09-10
updated: 2026-09-10
technology: Azure
difficulty: Beginner
tags:
  - dp-900
  - azure-sql
  - relational
aliases:
  - Azure SQL Deployment Choice
---

# 🧩 Choosing Between Azure SQL Database, Managed Instance, and SQL Server on a VM

> *The single most-tested relational-domain decision on DP-900.*

---

## 🎯 Problem

A scenario describes a SQL Server workload and asks which Azure product to deploy it on. Three options sit on the same IaaS → PaaS spectrum, and the exam expects you to pick based on specific signal words.

---

## 🤔 Mental Model

```
SQL Server on Azure VM   →   Azure SQL Managed Instance   →   Azure SQL Database
  (most control,               (near-100% SQL Server            (least admin,
   most admin work,             compatibility, still PaaS)        most managed,
   IaaS)                                                           PaaS)
```

Move left when the scenario needs **control or compatibility**. Move right when the scenario wants **less management overhead**.

---

## 🧠 Why This Pattern Works

Microsoft built these three tiers specifically to cover a migration continuum: apps that can't change at all (VM), apps that need most SQL Server features but can tolerate being managed (Managed Instance), and greenfield apps that want zero server management (SQL Database).

---

## 🔑 Decision Table

| Signal in the scenario | Answer |
|---|---|
| "Full control over the OS," "specific SQL Server version/feature not in PaaS" | SQL Server on Azure VM |
| "Lift and shift," "minimal code changes," "SQL Agent jobs," "cross-database queries" | Azure SQL Managed Instance |
| "New cloud-native app," "minimal admin," "auto-scale," "serverless" | Azure SQL Database |
| "Many small databases with unpredictable usage" | Azure SQL Database, Elastic Pool |

---

## 🚀 Common Use Cases

- Migration planning conversations
- Cost/management-overhead trade-off questions
- "Which service supports feature X" recall questions

---

## 🚨 Common Mistakes

- Picking Azure SQL Database for a workload that explicitly needs cross-database queries or SQL Agent — that's the Managed Instance tell
- Picking SQL Server on a VM by default "to be safe" — the exam rewards picking the *most managed* option that still satisfies the requirement
- Forgetting that Managed Instance is still PaaS (Microsoft patches the OS/engine), just with much higher compatibility than SQL Database

---

## 💡 Wisdom from Mímir

When in doubt, ask: "does this requirement force me left on the spectrum, or can I default to the most-managed option?" The exam almost always signals the forcing requirement explicitly — it doesn't expect you to guess.

---

## 🔗 Related Notes

- [[Relational Data on Azure]] — full concept writeup
- [[Core Data Concepts]]
- [[DP-900 Codex]]
