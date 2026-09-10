---
type: exam-scenario
status: active
created: 2026-09-10
updated: 2026-09-10
technology: Azure
difficulty: Beginner
tags:
  - az-900
  - cloud-concepts
aliases:
  - Cloud Deployment Model Choice
---

# 🧩 Public vs Private vs Hybrid vs Multi-Cloud

> *The first classification question the exam asks, and the easiest one to overthink.*

---

## 🎯 Problem

A scenario describes an organization's infrastructure setup. Classify it as public, private, hybrid, or multi-cloud.

---

## 🤔 Mental Model

| Signal in the scenario | Model |
|---|---|
| "third-party provider," "shared infrastructure," "no hardware to manage" | Public cloud |
| "dedicated infrastructure," "on-premises," "for our organization only" | Private cloud |
| "some workloads stay on-prem, others move to the cloud," "gradual migration" | Hybrid cloud |
| "uses both AWS and Azure" | Multi-cloud |

---

## 🧠 Why This Pattern Works

The deployment models describe *where the infrastructure lives and who else shares it*, not what service model (IaaS/PaaS/SaaS) is used on top of it. A private cloud can still run IaaS/PaaS/SaaS-style services internally — the two classifications are independent axes, which is exactly why the exam sometimes asks about both in the same scenario.

---

## 🚨 Common Mistakes

- Calling any use of two Azure regions "multi-cloud" — multi-cloud specifically means multiple *providers* (e.g. Azure + AWS), not multiple regions within one provider
- Assuming hybrid always means "temporary, until fully migrated" — many organizations run hybrid permanently for compliance/latency reasons

---

## 💡 Wisdom from Mímir

Ask two separate questions: "who owns the hardware?" (public/private/hybrid) and "how many providers are involved?" (single-cloud vs multi-cloud). Don't let one answer bleed into the other.

---

## 🔗 Related Notes

- [[Cloud Concepts]] — full concept writeup
- [[AZ-900 Codex]]
