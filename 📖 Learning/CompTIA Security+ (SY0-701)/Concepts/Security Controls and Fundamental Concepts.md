---
type: concept
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Security+
difficulty: Beginner
tags:
  - security-plus
  - general-security-concepts
aliases:
  - Security+ Control Types
  - Security+ Zero Trust
publish: true
permalink: comptia-security-sy0-701/security-controls-and-fundamental-concepts
---

# <span class="rune">ᛟ</span> Security Controls and Fundamental Concepts

> *Every security control on the exam can be described two ways at once: what it IS and what it DOES.*

---

## 🎯 Purpose

Covers 1.1-1.2 of the 12%-weighted General Security Concepts domain: types of security controls, and fundamental concepts (CIA, AAA, Zero Trust, gap analysis).

---

## 🧠 Key Ideas

- Every control has a **category** (technical/managerial/operational/physical — *what it is*) and a **type** (preventive/deterrent/detective/corrective/compensating/directive — *what it does*) — these are two independent axes
- The **CIA triad** (Confidentiality, Integrity, Availability) is the goal every other control ultimately serves; **non-repudiation** adds "and they can't deny it" on top
- **AAA** covers Authentication (who), Authorization (what they can do), Accounting (what they did) — applied to both people and systems
- **Zero Trust** splits into a **control plane** (adaptive identity, policy engine, policy administrator — the decision-making layer) and a **data plane** (policy enforcement point, implicit trust zones — where decisions get enforced)
- **Deception technology** (honeypot/honeynet/honeyfile/honeytoken) exists to detect and study attackers, not just to keep them out

---

## ⚙️ How It Works

### Control categories vs. types

| Category (what it is) | Type (what it does) |
|---|---|
| Technical | Preventive — stops an incident before it happens |
| Managerial | Deterrent — discourages an attempt |
| Operational | Detective — identifies an incident in progress/after the fact |
| Physical | Corrective — fixes the impact after an incident |
| | Compensating — an alternative when the primary control isn't feasible |
| | Directive — tells people what to do (policy) |

A single real-world control can be classified along *both* axes at once — e.g., a fence is a **physical** category, **deterrent** type control.

### Fundamental concepts

| Concept | Meaning |
|---|---|
| CIA triad | Confidentiality, Integrity, Availability |
| Non-repudiation | The originator of an action cannot credibly deny it |
| AAA | Authentication → Authorization → Accounting |
| Gap analysis | Compares current security posture against a desired target state |

### Zero Trust architecture

| Plane | Components |
|---|---|
| Control plane | Adaptive identity, threat scope reduction, policy-driven access control, Policy Administrator, Policy Engine |
| Data plane | Implicit trust zones, subject/system, Policy Enforcement Point |

Zero Trust's core principle: never trust by default, regardless of network location — every access request is evaluated against policy.

### Physical security and deception technology

| Category | Examples |
|---|---|
| Physical security | Bollards, access control vestibule, fencing, video surveillance, security guard, badges, lighting, sensors (infrared/pressure/microwave/ultrasonic) |
| Deception/disruption | Honeypot (decoy system), honeynet (decoy network), honeyfile (decoy file), honeytoken (decoy credential/data) |

---

## 💻 Examples

```text
"A security camera records activity for later review but doesn't stop an
intruder in real time."
→ Detective type, physical category

"A policy document requires employees to complete annual security
training."
→ Directive type, managerial category

"A compromised employee credential is used, but the system logs prove
exactly which actions that account took."
→ Non-repudiation

"An organization evaluates every access request based on identity and
context, regardless of whether the user is on the internal network."
→ Zero Trust

"A decoy file named 'passwords.xlsx' is placed to alert security if
anyone opens it."
→ Honeyfile
```

---

## 🚨 Common Mistakes

- Treating category and type as the same classification — a control needs both to be fully described
- Confusing deterrent (discourages attempts) with preventive (actually stops them) — a "Beware of Dog" sign deters, a locked door prevents
- Assuming Zero Trust means "no trust ever" in a way that blocks all access — it means *no implicit* trust; access is still granted based on verified policy
- Mixing up a honeypot (one decoy system) with a honeynet (an entire decoy network)

---

## 📖 Further Reading

- [CompTIA Security+ (SY0-701) official exam objectives](https://www.comptia.org/certifications/security)

---

## 🔗 Related Notes

- [[Change Management and Cryptographic Solutions]]
- [[Identifying the Right Security Control Type]] — drill note
- [[Applying Zero Trust and AAA Concepts]] — drill note
- [[Security+ Codex]]
