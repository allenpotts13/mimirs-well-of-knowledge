---
type: concept
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Network+
difficulty: Intermediate
tags:
  - network-plus
  - network-security
aliases:
  - Network+ Security Fundamentals
  - Network+ Compliance
publish: true
permalink: comptia-network-n10-009/network-security-fundamentals-and-compliance
---

# <span class="rune">ᛟ</span> Network Security Fundamentals and Compliance

> *Every acronym in this note (AAA, MFA, SSO, PKI, CIA) is a building block the rest of the Security domain assumes you already know.*

---

## 🎯 Purpose

Covers 4.1 of the 14%-weighted Network Security domain: basic network security concepts — logical/physical security, identity and access management, deception technologies, and regulatory compliance.

---

## 🧠 Key Ideas

- **Encryption** protects data in two states: **in transit** (moving across the network) and **at rest** (stored) — a solution for one doesn't automatically cover the other
- **PKI** uses certificate authorities to establish trust; a **self-signed** certificate skips that trusted third party, which is fine internally but triggers warnings publicly
- **AAA** — Authentication (who are you), Authorization (what can you do), Accounting (what did you do) — is the framework behind RADIUS, TACACS+, LDAP, and SAML
- **Least privilege** and **role-based access control** are the two core principles limiting what an authenticated user can actually touch
- The **CIA triad** (Confidentiality, Integrity, Availability) is the foundational goal-set that every other security control in this exam ultimately serves

---

## ⚙️ How It Works

### Encryption and certificates

| Concept | Detail |
|---|---|
| Data in transit | Encrypted while moving across the network (e.g., TLS) |
| Data at rest | Encrypted while stored (e.g., disk encryption) |
| PKI | Certificate authorities issue trusted certificates |
| Self-signed | No trusted third party — fine internally, triggers browser warnings publicly |

### Identity and access management (IAM)

| Term | Role |
|---|---|
| Authentication | Confirms identity (MFA, SSO all live here) |
| Authorization | Confirms permitted actions (least privilege, RBAC, geofencing) |
| MFA | Multiple verification factors |
| SSO | One login grants access across multiple systems |
| RADIUS | Centralized AAA, common for network device/Wi-Fi authentication |
| TACACS+ | Cisco-favored AAA, separates authentication/authorization/accounting more granularly than RADIUS |
| LDAP | Directory service protocol for identity lookups |
| SAML | XML-based standard enabling SSO between an identity provider and service providers |

### Physical security and deception technologies

| Category | Examples |
|---|---|
| Physical security | Cameras, locks |
| Deception technologies | Honeypot (one decoy system), honeynet (an entire decoy network) — both lure and study attackers |

### Common security terminology

| Term | Meaning |
|---|---|
| Risk | Potential for loss |
| Vulnerability | A weakness that could be exploited |
| Exploit | The method used to take advantage of a vulnerability |
| Threat | A potential source of harm |
| CIA triad | Confidentiality, Integrity, Availability |

### Compliance and segmentation

| Regulation | Focus |
|---|---|
| PCI DSS | Payment card data |
| GDPR | EU personal data privacy |

**Network segmentation enforcement** applies specifically to IoT/IIoT, SCADA/ICS/OT, guest, and BYOD devices — isolating them from critical infrastructure.

---

## 💻 Examples

```text
"A file is encrypted on disk but transmitted in plaintext over the
network."
→ Data at rest is protected, data in transit is not — two separate
controls needed

"An internal test server uses a certificate not issued by a trusted
public CA, and browsers warn accordingly."
→ Self-signed certificate

"A user logs into one portal and gains access to email, file storage,
and an internal wiki without logging in again."
→ SSO

"A security team sets up a decoy server designed to attract and study
attacker behavior."
→ Honeypot

"An IoT thermostat network must be isolated from the corporate financial
systems network."
→ Network segmentation enforcement
```

---

## 🚨 Common Mistakes

- Assuming encrypting data at rest also protects it in transit, or vice versa — both must be addressed separately
- Confusing authentication (who you are) with authorization (what you're allowed to do) — MFA strengthens the former, RBAC governs the latter
- Mixing up a honeypot (single decoy) with a honeynet (an entire decoy network)
- Treating SSO as inherently less secure — when paired with strong MFA, SSO can improve security by reducing password fatigue/reuse

---

## 📖 Further Reading

- [CompTIA Network+ (N10-009) official exam objectives](https://www.comptia.org/certifications/network)

---

## 🔗 Related Notes

- [[Network Attacks and Defense Techniques]]
- [[Network+ Codex]]
