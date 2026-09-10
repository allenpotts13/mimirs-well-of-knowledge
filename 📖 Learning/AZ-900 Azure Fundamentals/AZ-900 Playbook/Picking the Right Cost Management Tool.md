---
type: exam-scenario
status: published
created: 2026-09-10
updated: 2026-09-10
technology: Azure
difficulty: Beginner
tags:
  - az-900
  - cost-management
aliases:
  - Cost Tool Choice
publish: true
permalink: az-900-azure-fundamentals/picking-the-right-cost-management-tool
---

# <span class="rune">ᚲ</span> Picking the Right Cost Management Tool

> *Before you deploy, during, and after — Azure has a different tool for each moment, and the exam tests whether you know which moment you're in.*

---

## 🎯 Problem

A scenario describes a cost-related task at some point in a deployment's lifecycle. Pick the matching tool.

---

## 🤔 Mental Model

```
BEFORE deploying          AFTER deploying, ongoing
─────────────────         ────────────────────────
Pricing calculator         Azure Cost Management + Billing
TCO calculator              (budgets, alerts, actual spend analysis)
(compare to on-prem)
```

---

## 🔑 Decision Table

| Scenario signal | Tool |
|---|---|
| "Estimate the monthly cost of a planned set of Azure resources" | Pricing calculator |
| "Compare the cost of staying on-prem vs. moving to Azure" | TCO calculator |
| "Set a budget and get alerted when spend approaches it" | Azure Cost Management + Billing |
| "Analyze which resources are driving this month's bill" | Azure Cost Management + Billing |
| "Get a recommendation to resize or shut down an underused resource" | Azure Advisor (cost recommendations) |
| "Tag resources by department for chargeback reporting" | Tags, used with Cost Management |

---

## 🧠 Why This Pattern Works

The two calculators are planning tools — they answer "what would this cost" before money is spent, using no live data. Cost Management is an operational tool — it answers "what is this actually costing" using real usage data after deployment. Advisor sits alongside Cost Management as the recommendation engine that watches actual usage and suggests savings.

---

## 🚨 Common Mistakes

- Using the TCO calculator for a "what will this new cloud deployment cost" question — TCO specifically compares against an *existing on-prem* baseline; Pricing calculator is for a standalone estimate
- Forgetting tags aren't a cost tool by themselves — they're metadata that *Cost Management* uses to group/filter spend

---

## 💡 Wisdom from Mímir

Ask "has anything actually been deployed yet?" If no → a calculator. If yes → Cost Management (and Advisor for recommendations on top of it).

---

## 🔗 Related Notes

- [[Azure Management and Governance]] — full concept writeup
- [[CapEx vs OpEx and the Economics of Cloud]]
- [[AZ-900 Codex]]
