---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Security+
difficulty: Beginner
tags:
  - security-plus
  - general-security-concepts
aliases:
  - Change Management Gap Identification
publish: true
permalink: comptia-security-sy0-701/evaluating-change-management-impact-on-security
---

# <span class="rune">ᚲ</span> Evaluating Change Management Impact on Security

> *When a change goes wrong, the exam almost always wants you to name the specific missing step, not just say "poor planning."*

---

## 🎯 Problem

A scenario describes a change that caused a problem, or a proposed change lacking some element. Identify the missing change management component.

---

## 🤔 Mental Model

| Signal in the scenario | Missing element |
|---|---|
| "the change failed and there was no way to revert it" | Backout plan |
| "no one evaluated what else the change might affect" | Impact analysis |
| "the change was tested only in production" | Missing pre-production testing / sandbox |
| "no one approved the change before it happened" | Approval process |
| "diagrams/policies weren't updated after the change" | Documentation |
| "the change happened during business hours, disrupting users" | Maintenance window not respected |
| "a legacy application broke because of an unconsidered dependency" | Dependencies not accounted for in impact analysis |
| "the change wasn't tracked anywhere" | Version control |

---

## 🧠 Why This Pattern Works

Every change management failure scenario is really asking "which specific safeguard was skipped?" The safeguards cluster into three phases: **before** (approval, impact analysis, stakeholder sign-off, test results, maintenance window scheduling), **during** (following the standard operating procedure, respecting allow/deny lists and restricted activities), and **after** (updating documentation, version control). Identifying which phase the scenario's failure happened in narrows the answer immediately.

---

## 🚨 Common Mistakes

- Giving a generic answer like "bad planning" instead of naming the specific missing element (backout plan, impact analysis, etc.)
- Assuming approval alone is sufficient without impact analysis — a change can be approved and still poorly analyzed
- Forgetting documentation updates are part of change management, not an afterthought
- Overlooking legacy application dependencies as a real, testable impact-analysis failure mode

---

## 💡 Wisdom from Mímir

Sort the failure into before/during/after the change happened — that sort alone usually narrows the answer to one or two candidates.

---

## 🔗 Related Notes

- [[Change Management and Cryptographic Solutions]] — full concept writeup
- [[Security+ Codex]]
