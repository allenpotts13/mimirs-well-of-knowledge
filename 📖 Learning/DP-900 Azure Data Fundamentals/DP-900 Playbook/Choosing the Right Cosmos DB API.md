---
type: exam-scenario
status: published
created: 2026-09-10
updated: 2026-09-10
technology: Azure
difficulty: Beginner
tags:
  - dp-900
  - cosmos-db
  - nosql
aliases:
  - Cosmos DB API Choice
publish: true
permalink: dp-900-azure-data-fundamentals/choosing-the-right-cosmos-db-api
---

# <span class="rune">ᚲ</span> Choosing the Right Cosmos DB API

> *One engine, five faces — the exam wants you to match the data shape in the scenario to the API built for it.*

---

## 🎯 Problem

A scenario describes data (documents, key-value pairs, wide rows, or relationships) and asks which Cosmos DB API to use.

---

## 🤔 Mental Model

| Data shape described | API | NoSQL model |
|---|---|---|
| Flexible JSON documents, product catalogs, profiles | **Core (SQL) API** | Document |
| Existing MongoDB app/driver | **MongoDB API** | Document |
| Wide-column, time-series, existing Cassandra app | **Cassandra API** | Column-family |
| Relationships, connections, "friend of a friend," fraud rings | **Gremlin API** | Graph |
| Simple key-value lookups, existing Azure Table Storage app | **Table API** | Key-value |

---

## 🧠 Why This Pattern Works

Cosmos DB is one distributed database engine underneath, but it exposes different wire protocols/query languages so existing apps and data shapes map onto it naturally. The exam question is really asking "what's the data shape," then expecting you to know which API name matches that shape.

---

## 🚀 Common Use Cases

- "Migrate an existing MongoDB app to Azure with minimal changes" → MongoDB API
- "Model a social graph of connections between users" → Gremlin API
- "Store IoT time-series readings at massive scale" → Cassandra API
- "Simple session/shopping-cart storage" → Table API or Core (SQL) API

---

## 🚨 Common Mistakes

- Answering "Core (SQL) API" for graph/relationship scenarios just because "SQL" sounds familiar — the word "relationships" is the Gremlin signal, not SQL
- Forgetting Table API exists as a Cosmos DB option (it's easy to only remember it as a separate Azure Storage service)

---

## 💡 Wisdom from Mímir

Read for the noun, not the buzzword. "Relationships"/"connections"/"network" = graph = Gremlin, every time, regardless of what else is in the sentence.

---

## 🔗 Related Notes

- [[Non-Relational Data on Azure]] — full concept writeup
- [[DP-900 Codex]]
