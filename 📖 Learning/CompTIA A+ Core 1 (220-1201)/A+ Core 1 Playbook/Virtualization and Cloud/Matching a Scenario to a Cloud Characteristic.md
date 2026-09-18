---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA A+
difficulty: Beginner
tags:
  - a-plus-1201
  - cloud-computing
aliases:
  - Cloud Characteristic Identification
publish: true
permalink: comptia-a-core-1-220-1201/matching-a-scenario-to-a-cloud-characteristic
---

# <span class="rune">ᚲ</span> Matching a Scenario to a Cloud Characteristic

> *Cloud characteristics describe behavior, not infrastructure — that's what separates this from the deployment-model question.*

---

## 🎯 Problem

A scenario describes cloud behavior (scaling, billing, availability, data behavior). Identify the specific cloud characteristic being described.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "resources automatically increase during a traffic spike, then shrink back" | Elasticity |
| "bill is based on actual data transferred in and out" | Metered utilization (ingress/egress) |
| "provider guarantees a specific uptime percentage" | Availability |
| "multiple customers share the same underlying hardware, isolated from each other" | Multitenancy |
| "files stay consistent across a laptop, phone, and tablet automatically" | File synchronization |
| "one customer gets hardware no one else touches, vs. a shared pool" | Dedicated vs. shared resources |

---

## 🧠 Why This Pattern Works

These characteristics describe *behavior a customer experiences*, independent of which deployment model (public/private/hybrid) or service model (IaaS/PaaS/SaaS) is in play. A public cloud SaaS product and a private cloud IaaS deployment can both exhibit elasticity — the characteristic is about how the resource behaves under demand or billing, not what kind of cloud it is.

---

## 🚨 Common Mistakes

- Confusing elasticity (automatic, real-time scaling) with the more general idea of scalability (the capability to scale, not necessarily automatic)
- Assuming "shared resources" always means public cloud — a private cloud can still pool resources across internal departments
- Missing that metered utilization can apply to compute, storage, *and* data transfer separately — egress in particular often surprises people on a bill

---

## 💡 Wisdom from Mímir

When a scenario is about a bill or a resource changing size on its own, it's a characteristic question, not a deployment/service model question — don't reach for public/private/IaaS/PaaS answers here.

---

## 🔗 Related Notes

- [[Virtualization and Cloud Computing]] — full concept writeup
- [[A+ Core 1 Codex]]
