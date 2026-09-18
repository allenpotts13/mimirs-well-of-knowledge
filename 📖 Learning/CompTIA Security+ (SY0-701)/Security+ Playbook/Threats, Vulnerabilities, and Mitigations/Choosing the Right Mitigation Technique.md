---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Security+
difficulty: Intermediate
tags:
  - security-plus
  - threats-vulnerabilities
aliases:
  - Mitigation Technique Choice
publish: true
permalink: comptia-security-sy0-701/choosing-the-right-mitigation-technique
---

# <span class="rune">ᚲ</span> Choosing the Right Mitigation Technique

> *Reduce the attack surface first — everything else is defense-in-depth on top of that.*

---

## 🎯 Problem

A scenario describes a security gap. Choose the correct mitigation technique to close it.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "a compromised system should be contained without full isolation from the network" | Segmentation |
| "a system should be completely cut off during an active incident" | Isolation |
| "only pre-approved software may execute" | Application allow list |
| "known vulnerabilities need to be closed" | Patching |
| "data should remain protected even if accessed" | Encryption |
| "ongoing visibility into system behavior is needed" | Monitoring |
| "users/systems should only have the access strictly necessary" | Least privilege |
| "systems should be verified against a known-good state" | Configuration enforcement |
| "an old, unused system should be retired entirely" | Decommissioning |
| "unused services/ports on a server are a needless risk" | Hardening (disable unused ports/services) |
| "default vendor credentials are still active" | Hardening (change default passwords) |

---

## 🧠 Why This Pattern Works

Mitigation techniques layer from broad to specific: **segmentation/isolation** limit blast radius at the architecture level, **access control/least privilege** limit blast radius at the identity level, **hardening** reduces what's exposed on any given system, and **patching/monitoring/configuration enforcement** are ongoing maintenance practices that keep all of the above effective over time. A scenario's specific gap usually maps to exactly one of these layers.

---

## 🚨 Common Mistakes

- Confusing segmentation (limits spread, system stays partially connected) with isolation (fully cuts a system off)
- Treating patching and configuration enforcement as the same activity — patching closes known vulnerabilities, configuration enforcement ensures settings match a baseline
- Assuming hardening is a one-time setup step rather than an ongoing practice as new unused services/ports appear
- Reaching for encryption when the real gap is about *who* can access something, not whether the data itself is protected — that's access control/least privilege instead

---

## 💡 Wisdom from Mímir

Ask "does this reduce what's exposed, limit who can reach it, or limit how far a compromise spreads?" Each answer points to a different mitigation family.

---

## 🔗 Related Notes

- [[Attack Indicators and Mitigation Techniques]] — full concept writeup
- [[Security+ Codex]]
