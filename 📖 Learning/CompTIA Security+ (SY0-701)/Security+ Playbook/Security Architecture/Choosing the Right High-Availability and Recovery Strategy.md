---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Security+
difficulty: Intermediate
tags:
  - security-plus
  - security-architecture
aliases:
  - HA and Recovery Choice
publish: true
permalink: comptia-security-sy0-701/choosing-the-right-high-availability-and-recovery-strategy
---

# <span class="rune">ᚲ</span> Choosing the Right High-Availability and Recovery Strategy

> *A hot site plus active-active is the only combination that gets you close to zero downtime — everything else is a trade-off away from that.*

---

## 🎯 Problem

A scenario describes an availability or recovery requirement. Choose the correct site type, availability model, or backup approach.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "backup site is fully mirrored, near-instant failover" | Hot site + active-active |
| "backup site has some equipment but needs time to activate" | Warm site |
| "backup site is just space, requires full setup before use" | Cold site |
| "all nodes actively serve traffic at once" | Active-active |
| "one node stands by, only takes over on failure" | Active-passive |
| "backup plan needs to survive a fire destroying the primary site" | Offsite backup copy |
| "recovery testing without actually failing anything over" | Tabletop exercise |
| "recovery testing that actually fails systems over" | Simulation / failover test |
| "resources are spread across geographically distant locations for resilience" | Geographic dispersion |
| "avoid depending on a single vendor's technology stack" | Platform diversity |

---

## 🧠 Why This Pattern Works

Site tier (cold/warm/hot) and availability model (active-active/active-passive) both trade cost against recovery speed — the more instantaneous the failover needs to be, the more expensive and fully-provisioned the solution must be. Backup strategy (onsite/offsite, frequency, encryption) and testing type (tabletop vs. simulation) are separate, complementary decisions layered on top of whatever site/availability model is chosen.

---

## 🚨 Common Mistakes

- Recommending a warm site for a near-zero-downtime requirement — only a hot site with active-active realistically achieves that
- Assuming a tabletop exercise validates that failover actually works — only an actual simulation/failover test confirms that
- Treating geographic dispersion and multi-cloud as the same technique — dispersion is about physical location, multi-cloud is about provider diversity
- Forgetting an offsite backup copy is specifically what protects against site-level disasters, regardless of how good onsite redundancy is

---

## 💡 Wisdom from Mímir

If the scenario's tolerance for downtime is near zero, only hot + active-active satisfies it — resist the temptation to recommend a cheaper option that doesn't actually meet the stated requirement.

---

## 🔗 Related Notes

- [[Data Protection, Resilience, and Recovery]] — full concept writeup
- [[Security+ Codex]]
