---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Security+
difficulty: Beginner
tags:
  - security-plus
  - threats-vulnerabilities
aliases:
  - Threat Actor Classification
publish: true
permalink: comptia-security-sy0-701/identifying-threat-actor-types-and-motivations
---

# <span class="rune">ᚲ</span> Identifying Threat Actor Types and Motivations

> *Resources and sophistication narrow the actor. Motivation confirms it.*

---

## 🎯 Problem

A scenario describes an attacker's behavior, resources, or goal. Identify the threat actor type and motivation.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "highly resourced, long-term, targets critical infrastructure" | Nation-state, motivated by espionage/war/disruption |
| "financially motivated, well-organized, operates like a business" | Organized crime |
| "motivated by a political or social cause" | Hacktivist |
| "has legitimate internal access, motive is personal" | Insider threat |
| "low skill, uses pre-built tools, motivated by curiosity/chaos" | Unskilled attacker |
| "employee deploys unauthorized cloud tools for convenience" | Shadow IT |
| "attacker demands payment to not release stolen data" | Financial gain, possibly organized crime or an unskilled attacker using ransomware-as-a-service |

---

## 🧠 Why This Pattern Works

Resources and sophistication act as a filter: nation-states and organized crime have significant funding and capability, while unskilled attackers and hacktivists vary widely but typically lack nation-state-level resources. Motivation is the confirming signal — espionage and war point to nation-states, financial gain points to organized crime (or opportunistic insiders), and political/social causes point to hacktivists.

---

## 🚨 Common Mistakes

- Assuming all high-impact attacks must be nation-state — organized crime can be equally well-resourced and more common
- Treating insider threats as requiring technical sophistication — access and motive matter more than skill level
- Labeling shadow IT as a malicious actor category — it's an internal risk from otherwise well-intentioned behavior
- Assuming hacktivists are always "unskilled" — sophistication varies widely within this category

---

## 💡 Wisdom from Mímir

Resources narrow the list. Motivation confirms the choice. Use both signals together, never just one.

---

## 🔗 Related Notes

- [[Threat Actors and Attack Vectors]] — full concept writeup
- [[Security+ Codex]]
