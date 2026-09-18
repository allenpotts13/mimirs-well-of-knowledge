---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Network+
difficulty: Beginner
tags:
  - network-plus
  - networking-concepts
  - cloud-computing
aliases:
  - Network+ Cloud Model Choice
publish: true
permalink: comptia-network-n10-009/choosing-the-right-cloud-service-and-deployment-model
---

# <span class="rune">ᚲ</span> Choosing the Right Cloud Service and Deployment Model

> *Two axes, not one: what layer you manage, and who else shares the infrastructure.*

---

## 🎯 Problem

A scenario describes a cloud requirement. Identify the correct service model, deployment model, or connectivity option.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "manage only the OS, runtime, and app" | IaaS |
| "manage only the application and data" | PaaS |
| "manage nothing but configuration/data" | SaaS |
| "dedicated to one organization only" | Private cloud |
| "shared, third-party infrastructure" | Public cloud |
| "combination of public and private" | Hybrid cloud |
| "resources scale automatically with demand" | Elasticity |
| "dedicated, non-internet circuit to a cloud provider" | Direct Connect |
| "encrypted connection to the cloud over the public internet" | VPN |
| "isolated private network segment within a public cloud" | VPC |

---

## 🧠 Why This Pattern Works

Service model (IaaS/PaaS/SaaS) answers "how much do I manage?" Deployment model (public/private/hybrid) answers "who else shares this infrastructure?" These are independent axes — a scenario can combine them (e.g., a private-cloud IaaS deployment). Connectivity options (VPN vs. Direct Connect) are a third, separate axis about *how* you reach the cloud, trading cost and setup complexity for consistency and privacy.

---

## 🚨 Common Mistakes

- Treating deployment model and service model as the same question — they answer different things and can combine in any way
- Assuming a VPN to the cloud is as reliable/low-latency as Direct Connect — Direct Connect is a dedicated circuit specifically because internet-based VPN can't guarantee that
- Confusing a VPC (an isolated network segment inside a cloud) with a VPN (an encrypted tunnel) — different concepts despite similar names

---

## 💡 Wisdom from Mímir

Ask "what do they manage?" for service model, then separately ask "who else is on this hardware?" for deployment model — never let one answer bleed into the other.

---

## 🔗 Related Notes

- [[OSI Model, Appliances, and Cloud Concepts]] — full concept writeup
- [[Network+ Codex]]
