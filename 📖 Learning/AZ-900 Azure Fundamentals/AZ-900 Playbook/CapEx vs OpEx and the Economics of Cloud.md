---
type: exam-scenario
status: published
created: 2026-09-10
updated: 2026-09-10
technology: Azure
difficulty: Beginner
tags:
  - az-900
  - cloud-concepts
  - cost
aliases:
  - CapEx vs OpEx
publish: true
permalink: az-900-azure-fundamentals/capex-vs-opex-and-the-economics-of-cloud
---

# <span class="rune">ᚲ</span> CapEx vs OpEx and the Economics of Cloud

> *The financial argument for cloud adoption, boiled down to one contrast the exam tests directly.*

---

## 🎯 Problem

A scenario describes a spending pattern (upfront hardware purchase vs. ongoing subscription) and asks whether it's CapEx or OpEx, or asks which model cloud computing represents.

---

## 🤔 Mental Model

| | CapEx | OpEx |
|---|---|---|
| Meaning | Capital Expenditure — upfront investment in physical assets | Operational Expenditure — ongoing, pay-as-you-go spending |
| Cloud fit | On-premises datacenters | Azure consumption-based billing |
| Risk profile | Over/under-provisioning locked in for years | Scales with actual usage |

---

## 🧠 Why This Pattern Works

Buying servers is a capital purchase — a large sunk cost, depreciated over years, sized for peak demand you might rarely hit. Cloud consumption billing converts that into an operational expense: you pay only for what you use, when you use it, and can scale down if demand drops. This is the core financial pitch for cloud migration, which is why the exam tests it directly rather than assuming you'll infer it.

---

## 🚀 Common Use Cases

- "A company sells its datacenter hardware and moves workloads to Azure, paying monthly based on usage." → shift from CapEx to OpEx
- "A company estimates the cost difference between running its own datacenter versus Azure over 5 years." → TCO calculator (see [[Picking the Right Cost Management Tool]])

---

## 🚨 Common Mistakes

- Assuming cloud spending is automatically cheaper — the exam frames it as more *predictable and elastic*, not inherently lower cost
- Forgetting that Azure Reserved Instances (paying upfront for a 1-3 year commitment at a discount) reintroduces a CapEx-like element into an otherwise OpEx model — it's a nuance worth knowing but rarely the point of a basic question

---

## 💡 Wisdom from Mímir

If the scenario mentions buying, owning, or depreciating hardware, it's CapEx. If it mentions a recurring bill tied to actual usage, it's OpEx. Cloud computing itself is the textbook OpEx example.

---

## 🔗 Related Notes

- [[Cloud Concepts]] — full concept writeup
- [[AZ-900 Codex]]
