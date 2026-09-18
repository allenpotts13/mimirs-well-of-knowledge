---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Network+
difficulty: Intermediate
tags:
  - network-plus
  - network-operations
aliases:
  - Disaster Recovery Choice
publish: true
permalink: comptia-network-n10-009/choosing-the-right-disaster-recovery-approach
---

# <span class="rune">ᚲ</span> Choosing the Right Disaster Recovery Approach

> *RPO asks "how much data can we lose?" RTO asks "how long can we be down?" Never answer one with the other's math.*

---

## 🎯 Problem

A scenario states a business continuity requirement or DR test description. Identify the correct metric, site type, or approach.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "at most 15 minutes of data loss is acceptable" | RPO of 15 minutes |
| "at most 2 hours of downtime is acceptable" | RTO of 2 hours |
| "average time to fix a failure once it occurs" | MTTR |
| "average time between failures, a reliability measure" | MTBF |
| "backup site is empty space only, no equipment" | Cold site |
| "backup site has some equipment, data not fully current" | Warm site |
| "backup site fully mirrors production in near-real-time" | Hot site |
| "all nodes actively serve traffic simultaneously" | Active-active |
| "one node stands by, taking over only on failure" | Active-passive |
| "DR plan is tested by discussion only, nothing is actually failed over" | Tabletop exercise |
| "DR plan is tested by actually failing systems over" | Validation test |

---

## 🧠 Why This Pattern Works

RPO and RTO are set from the *business* perspective before any technical solution is chosen — they define the target, and the site tier (cold/warm/hot) and availability model (active-active/active-passive) are the technical means to hit that target. A very low RPO/RTO (near-zero data loss, near-zero downtime) requires a hot site with active-active — anything less won't meet the target. DR testing type (tabletop vs. validation) is a separate axis about *how confident* you are the plan actually works.

---

## 🚨 Common Mistakes

- Swapping RPO and RTO — RPO is about data (a point in time), RTO is about time-to-restore (a duration)
- Assuming a warm site is "good enough" for a near-zero RTO requirement — only a hot site with active-active can realistically meet that
- Treating a tabletop exercise as sufficient validation that failover actually works — only a validation test confirms that
- Confusing MTTR (how long to fix, lower is better) with MTBF (how long between failures, higher is better) — opposite directions of "better"

---

## 💡 Wisdom from Mímir

When a scenario gives you a number and a time unit, decide first whether it's measuring *lost data* (RPO) or *lost time* (RTO) — everything else about site tier and availability model follows from that single distinction.

---

## 🔗 Related Notes

- [[Network Documentation, Change Management, and Disaster Recovery]] — full concept writeup
- [[Network+ Codex]]
