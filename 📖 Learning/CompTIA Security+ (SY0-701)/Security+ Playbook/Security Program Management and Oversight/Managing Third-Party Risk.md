---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Security+
difficulty: Intermediate
tags:
  - security-plus
  - security-program-management
aliases:
  - Vendor Agreement Choice
publish: true
permalink: comptia-security-sy0-701/managing-third-party-risk
---

# <span class="rune">ᚲ</span> Managing Third-Party Risk

> *MSA, SOW, NDA, MOU — four contracts that get signed with the same vendor, each covering something different.*

---

## 🎯 Problem

A scenario describes a vendor relationship need. Choose the correct agreement type or vendor risk activity.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "overarching terms governing the entire vendor relationship" | MSA |
| "a specific, individual project's scope and deliverables" | SOW / WO |
| "confidentiality obligations between two parties" | NDA |
| "documented service performance commitments" | SLA |
| "a formal but less binding mutual understanding" | MOU |
| "a more formal agreement on a specific matter between parties" | MOA |
| "contractual right to inspect a vendor's security controls" | Right-to-audit clause |
| "vetting a vendor before signing anything" | Due diligence |
| "checking whether a vendor's own suppliers introduce risk" | Supply chain analysis |
| "ongoing check-ins with a vendor's security posture after signing" | Vendor monitoring |

---

## 🧠 Why This Pattern Works

Vendor agreements layer just like data protection concepts do: the MSA sets the umbrella terms once, SOWs/WOs define individual pieces of work under that umbrella, NDAs protect confidentiality throughout, and SLAs commit to specific performance levels. Vendor *risk activities* (due diligence, right-to-audit, supply chain analysis, monitoring) happen at different points in the relationship — before signing (due diligence), built into the contract (right-to-audit), and continuously afterward (monitoring).

---

## 🚨 Common Mistakes

- Using an SOW where an MSA is needed (or vice versa) — SOW is project-specific, MSA is relationship-wide
- Treating an MOU as equally binding as an MOA — MOUs are typically less formal
- Skipping supply chain analysis, assuming a vendor's own security posture is sufficient without considering *their* suppliers
- Forgetting vendor risk management doesn't end at signing — ongoing monitoring is part of the process

---

## 💡 Wisdom from Mímir

Ask "is this about the whole relationship, one project, confidentiality, or performance?" That question sorts MSA/SOW/NDA/SLA immediately.

---

## 🔗 Related Notes

- [[Third-Party Risk and Compliance]] — full concept writeup
- [[Security+ Codex]]
