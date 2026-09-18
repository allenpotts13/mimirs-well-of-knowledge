---
type: concept
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Security+
difficulty: Intermediate
tags:
  - security-plus
  - security-program-management
aliases:
  - Security+ Third-Party Risk
  - Security+ Compliance
publish: true
permalink: comptia-security-sy0-701/third-party-risk-and-compliance
---

# <span class="rune">ᛟ</span> Third-Party Risk and Compliance

> *An MSA, an SOW, an NDA, and an SLA all get signed with the same vendor — and each one covers something completely different.*

---

## 🎯 Purpose

Covers 5.3-5.4 of the Security Program Management and Oversight domain: third-party risk assessment/management, and elements of effective security compliance.

---

## 🧠 Key Ideas

- Vendor assessment (penetration testing, right-to-audit clauses, independent assessments, supply chain analysis) happens *before and during* a relationship; vendor selection (due diligence, conflict of interest checks) happens *before* signing
- Each agreement type serves a different legal purpose — memorize what each one actually governs, not just its acronym
- Compliance has both **reporting** (internal/external) and **monitoring** (due diligence/care, attestation, automation) components — reporting is periodic, monitoring is ongoing
- Non-compliance consequences aren't just financial — fines, sanctions, reputational damage, loss of license, and contractual impacts all apply
- Privacy adds a data-subject-centric layer on top of general compliance: **right to be forgotten**, data ownership, and controller-vs-processor responsibility all matter specifically for personal data

---

## ⚙️ How It Works

### Vendor assessment and selection

| Activity | Purpose |
|---|---|
| Penetration testing | Actively tests the vendor's security |
| Right-to-audit clause | Contractually guarantees the ability to audit the vendor |
| Independent assessments | Third-party verification, not self-reported |
| Supply chain analysis | Examines the vendor's own upstream risk |
| Due diligence | Research before entering the relationship |
| Conflict of interest | Checking for compromised objectivity |

### Agreement types (memorize what each governs)

| Agreement | Governs |
|---|---|
| SLA | Service performance commitments |
| MOA | Formal agreement between parties on a specific matter |
| MOU | Mutual understanding, less formally binding than an MOA |
| MSA | Overarching terms governing the whole relationship |
| WO / SOW | Specific work to be performed |
| NDA | Confidentiality obligations |
| BPA | Formal partnership terms between businesses |

**Ongoing oversight:** vendor monitoring, questionnaires, rules of engagement (especially for testing activities).

### Compliance elements

| Category | Detail |
|---|---|
| Compliance reporting | Internal vs. external |
| Non-compliance consequences | Fines, sanctions, reputational damage, loss of license, contractual impacts |
| Compliance monitoring | Due diligence/care, attestation and acknowledgement, internal and external checks, automation |

### Privacy

| Concept | Meaning |
|---|---|
| Legal implications | Local/regional, national, global privacy law |
| Data subject | The individual the data is about |
| Controller vs. processor | Who decides vs. who acts |
| Data inventory and retention | Knowing what personal data exists and for how long |
| Right to be forgotten | A data subject's right to have their data deleted |

---

## 💻 Examples

```text
"A contract broadly governs the overall vendor relationship, with
individual projects defined separately."
→ MSA (Master Service Agreement), with individual SOWs per project

"A company wants contractual assurance it can inspect a vendor's security
controls directly."
→ Right-to-audit clause

"Two parties want to formally document a shared understanding without a
fully binding contract."
→ MOU

"A customer requests their personal data be permanently deleted from a
company's systems."
→ Right to be forgotten

"A company fails a regulatory audit and faces a formal penalty."
→ Non-compliance consequence — fines/sanctions
```

---

## 🚨 Common Mistakes

- Treating MOA and MOU as identical — an MOU is generally less formally binding than an MOA
- Confusing an MSA (overarching terms) with an SOW (specific, individual work) — they're often used together, not interchangeably
- Assuming compliance monitoring is the same activity as compliance reporting — monitoring is ongoing, reporting is periodic communication of status
- Overlooking that the right to be forgotten is a specific *privacy* right, not a general data retention practice

---

## 📖 Further Reading

- [CompTIA Security+ (SY0-701) official exam objectives](https://www.comptia.org/certifications/security)

---

## 🔗 Related Notes

- [[Security Governance and Risk Management]]
- [[Audits, Assessments, and Security Awareness]]
- [[Managing Third-Party Risk]] — drill note
- [[Understanding Security Compliance Elements]] — drill note
- [[Security+ Codex]]
