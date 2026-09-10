---
type: exam-scenario
status: published
created: 2026-09-10
updated: 2026-09-10
technology: Azure
difficulty: Beginner
tags:
  - dp-900
  - oltp
  - olap
  - core-concepts
aliases:
  - OLTP vs OLAP Recognition
publish: true
---

# 🧩 Spotting OLTP vs OLAP in a Scenario

> *The most repeated pattern-match on the exam, dressed up in a dozen different wordings.*

---

## 🎯 Problem

A scenario describes a system's data operations. Classify it as transactional (OLTP) or analytical (OLAP).

---

## 🤔 Mental Model

Ask: is this system **running** the business right now, or **analyzing** what the business already did?

| Signal | Classification |
|---|---|
| "processes orders," "updates inventory," "checkout," "booking" | OLTP |
| "reports on," "trends," "dashboard," "historical," "aggregates" | OLAP |
| Many small, fast read/writes | OLTP |
| Few large, complex reads over lots of history | OLAP |
| Normalized schema | OLTP |
| Star/snowflake schema, fact + dimension tables | OLAP |

---

## 🧠 Why This Pattern Works

OLTP systems are optimized for correctness and speed on individual records because real transactions (a sale, a booking) depend on them. OLAP systems are optimized for scanning huge volumes because a report needs to summarize, not update, the past. The design goals are opposite, which is why the schemas end up opposite too (normalized vs. denormalized).

---

## 🚀 Common Use Cases

- "An e-commerce site processes payment transactions" → OLTP
- "A quarterly report aggregates two years of sales by region" → OLAP
- "A banking app posts thousands of transfers per second" → OLTP
- "An executive dashboard shows year-over-year revenue trends" → OLAP

---

## 🚨 Common Mistakes

- Assuming "database" always means OLTP — the word alone doesn't tell you, the *operation* does
- Missing that a single company usually has both: an OLTP system feeding an OLAP warehouse downstream

---

## 💡 Wisdom from Mímir

If the scenario has a customer waiting on the result (buying something, booking something), it's OLTP. If the scenario has an analyst or executive waiting on the result, it's OLAP.

---

## 🔗 Related Notes

- [[Core Data Concepts]] — full concept writeup
- [[DP-900 Codex]]
