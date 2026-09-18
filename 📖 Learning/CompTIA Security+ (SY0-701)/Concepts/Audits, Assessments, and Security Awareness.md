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
  - Security+ Audits
  - Security+ Penetration Testing
  - Security+ Security Awareness
publish: true
permalink: comptia-security-sy0-701/audits-assessments-and-security-awareness
---

# <span class="rune">ᛟ</span> Audits, Assessments, and Security Awareness

> *A pen tester who knows the network in advance is testing something different than one who knows nothing — and both are valid, for different reasons.*

---

## 🎯 Purpose

Covers 5.5-5.6 of the Security Program Management and Oversight domain: types/purposes of audits and assessments, and implementing security awareness practices (the latter a "given a scenario" objective).

---

## 🧠 Key Ideas

- Audits split into **internal** (compliance checks, self-assessments, audit committee) and **external** (regulatory, examinations, independent third-party) — each carries different weight and objectivity
- Penetration testing environment knowledge is a spectrum: **known environment** (full information, like white-box), **partially known**, and **unknown environment** (no information, like black-box)
- Pen testing style also splits into **offensive** (attacking), **defensive** (responding), and **integrated** (both teams working together)
- **Reconnaissance** during a pen test is either **passive** (no direct interaction with the target, e.g., OSINT) or **active** (direct interaction, e.g., port scanning) — passive is stealthier but yields less
- Security awareness isn't a single training event — it's phishing campaigns, ongoing guidance/training, anomalous behavior recognition, and a reporting/monitoring cycle that repeats

---

## ⚙️ How It Works

### Audit types

| Type | Examples |
|---|---|
| Internal | Compliance checks, audit committee review, self-assessments |
| External | Regulatory audits, examinations, independent third-party audits |
| Attestation | Formal confirmation of a claim's accuracy |

### Penetration testing

| Dimension | Options |
|---|---|
| Scope/style | Physical, offensive, defensive, integrated |
| Environment knowledge | Known (full info), partially known, unknown (no info) |
| Reconnaissance | Passive (no direct target interaction, e.g., OSINT) vs. active (direct interaction, e.g., scanning) |

### Security awareness practices

| Category | Activities |
|---|---|
| Phishing | Simulated campaigns, teaching recognition, defining response to reported messages |
| Anomalous behavior recognition | Risky, unexpected, and unintentional behavior categories |
| User guidance and training | Policy/handbooks, situational awareness, insider threat awareness, password management, removable media handling, social engineering awareness, operational security, hybrid/remote work considerations |
| Reporting and monitoring | Initial and recurring cycles |
| Development and execution | Building and running the awareness program itself |

---

## 💻 Examples

```text
"A tester is given full network diagrams and credentials before testing
begins."
→ Known environment penetration test

"A tester gathers information about a target using only public sources,
without ever touching the target's systems."
→ Passive reconnaissance

"A company runs simulated phishing emails to employees and tracks who
reports them."
→ Phishing campaign, part of security awareness

"An employee's account shows a login at 3 AM from an unusual location, an
example of behavior that warrants review even without confirmed malice."
→ Anomalous (unexpected) behavior recognition

"A regulator conducts a formal review of an organization's compliance
posture."
→ External/regulatory audit
```

---

## 🚨 Common Mistakes

- Confusing a known-environment test with a defensive test — environment knowledge and offensive/defensive role are two separate dimensions
- Assuming passive reconnaissance is "less useful" rather than recognizing it's chosen specifically to avoid detection
- Treating security awareness as a once-a-year training event rather than an ongoing cycle of training, phishing simulation, and monitoring
- Overlooking self-assessments as a valid (if less objective) form of internal audit

---

## 📖 Further Reading

- [CompTIA Security+ (SY0-701) official exam objectives](https://www.comptia.org/certifications/security)

---

## 🔗 Related Notes

- [[Third-Party Risk and Compliance]]
- [[Choosing the Right Audit or Assessment Type]] — drill note
- [[Implementing Security Awareness Practices]] — drill note
- [[Security+ Codex]]
