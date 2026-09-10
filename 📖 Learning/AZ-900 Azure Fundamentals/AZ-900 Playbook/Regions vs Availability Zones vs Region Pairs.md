---
type: exam-scenario
status: published
created: 2026-09-10
updated: 2026-09-10
technology: Azure
difficulty: Beginner
tags:
  - az-900
  - regions
  - availability-zones
  - resilience
aliases:
  - Azure Physical Structure
publish: true
---

# <span class="rune">ᚲ</span> Regions vs Availability Zones vs Region Pairs

> *Three different scopes of "spread things out so one failure doesn't take everything down" — the exam wants the right scope for the right threat.*

---

## 🎯 Problem

A scenario describes a resilience requirement (protect against a rack failure, a datacenter failure, or a whole-region disaster) and asks which Azure construct addresses it.

---

## 🤔 Mental Model

```
Datacenter  ⊂  Availability Zone  ⊂  Region  ⊂  Geography
   (one building)   (1+ datacenters,     (a set of AZs/       (a market, e.g.
                      independent power)   datacenters)         "United States")

Region Pair = two specific regions within the same geography,
              linked for disaster recovery
```

---

## 🔑 Decision Table

| Scenario signal | Construct |
|---|---|
| "Protect against a single datacenter failure within the same city/metro" | Availability Zones |
| "Deploy resources close to users in Europe vs. Asia" | Regions |
| "Recover if an entire region goes offline" | Region pair (failover to the paired region) |
| "Meet a legal requirement that data stays within a specific country" | Geography / data residency |

---

## 🧠 Why This Pattern Works

Each construct protects against a failure at a different physical scale. Availability zones solve "the building lost power." Region pairs solve "the whole region is unavailable." Microsoft deliberately staggers platform updates across paired regions (never both at once) specifically so a bad update can't take down both halves of your DR setup simultaneously.

---

## 🚨 Common Mistakes

- Using "region" and "availability zone" interchangeably — an AZ is a subdivision *within* a region, not a synonym for it
- Assuming every region has availability zones — not all regions support them
- Forgetting region pairs are geography-bound (e.g. two US regions pair with each other, not with a European region)

---

## 💡 Wisdom from Mímir

Match the blast radius in the scenario to the construct: building-level → AZ, region-level → region pair, country-level → geography/data residency.

---

## 🔗 Related Notes

- [[Core Azure Architecture and Services]] — full concept writeup
- [[AZ-900 Codex]]
