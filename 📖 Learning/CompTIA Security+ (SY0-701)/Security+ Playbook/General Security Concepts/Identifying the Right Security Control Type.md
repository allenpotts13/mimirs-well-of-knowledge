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
  - Security Control Classification
publish: true
permalink: comptia-security-sy0-701/identifying-the-right-security-control-type
---

# <span class="rune">ᚲ</span> Identifying the Right Security Control Type

> *Every control gets two labels, not one — classify the category, then classify the type, separately.*

---

## 🎯 Problem

A scenario describes a security measure. Classify its category (technical/managerial/operational/physical) and type (preventive/deterrent/detective/corrective/compensating/directive).

---

## 🤔 Mental Model

| Signal in the scenario | Category | Type |
|---|---|---|
| "a firewall blocks traffic" | Technical | Preventive |
| "warning sign at a facility entrance" | Physical | Deterrent |
| "security camera reviewed after an incident" | Physical | Detective |
| "restoring a system from backup after ransomware" | Technical/Operational | Corrective |
| "a written policy requiring annual training" | Managerial | Directive |
| "an alternate control used because the primary isn't feasible" | Varies | Compensating |
| "an IDS alerts on suspicious traffic" | Technical | Detective |
| "background checks before hiring" | Managerial | Preventive |

---

## 🧠 Why This Pattern Works

Category answers "what kind of control is this" (its nature), while type answers "what does it do in the security lifecycle" (before, during, or after an event, or as an alternative). A scenario can be classified along both axes independently — don't assume a technical control can't also be a deterrent, or that a physical control can't be preventive.

---

## 🚨 Common Mistakes

- Giving only one classification when the question asks for both category and type
- Confusing corrective (fixes the impact after) with compensating (an alternative control used instead of the primary one)
- Assuming all technical controls are preventive — many (like IDS, SIEM) are detective
- Labeling a policy as "preventive" when its function is directive — it *tells* people what to do, it doesn't technically block anything itself

---

## 💡 Wisdom from Mímir

Ask "what is this made of?" for category, then "when in the timeline does it act — before, during, after, or instead?" for type.

---

## 🔗 Related Notes

- [[Security Controls and Fundamental Concepts]] — full concept writeup
- [[Security+ Codex]]
