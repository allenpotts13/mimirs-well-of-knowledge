---
type: concept
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Security+
difficulty: Intermediate
tags:
  - security-plus
  - security-operations
aliases:
  - Security+ Hardening
  - Security+ Asset Management
publish: true
permalink: comptia-security-sy0-701/hardening-and-asset-management
---

# <span class="rune">ᛟ</span> Hardening and Asset Management

> *An asset you don't know exists is an asset you can't secure — and a device with default settings is barely secured at all.*

---

## 🎯 Purpose

Covers 4.1-4.2 of the 28%-weighted Security Operations domain — the single largest domain on the exam: applying security techniques to computing resources (a "given a scenario" objective) and the security implications of hardware/software/data asset management.

---

## 🧠 Key Ideas

- A **secure baseline** goes through a lifecycle: establish → deploy → maintain — it's not a one-time setup
- Hardening targets span a huge range: mobile devices, workstations, network gear (switches/routers), cloud infrastructure, servers, and specialized/constrained systems (ICS/SCADA, embedded, RTOS, IoT)
- Wireless security specifically layers **installation planning** (site surveys, heat maps), **security settings** (WPA3, AAA/RADIUS), and **mobile deployment models** (BYOD, COPE, CYOD) — each with different security/control trade-offs
- Asset management is a full lifecycle: **acquisition** → **assignment/tracking** (ownership, classification, inventory, enumeration) → **disposal/decommissioning** (sanitization, destruction, certification, retention)
- Application security techniques (input validation, secure cookies, static code analysis, code signing, sandboxing) defend the software itself, distinct from hardening the infrastructure it runs on

---

## ⚙️ How It Works

### Secure baselines and hardening targets

| Concept | Detail |
|---|---|
| Baseline lifecycle | Establish → Deploy → Maintain |
| Hardening targets | Mobile devices, workstations, switches, routers, cloud infrastructure, servers, ICS/SCADA, embedded systems, RTOS, IoT devices |

### Mobile device deployment models

| Model | Meaning |
|---|---|
| BYOD | Employee-owned device, enrolled under policy |
| COPE | Corporate-owned, personally enabled |
| CYOD | Employee chooses from a company-approved device list |

**Connection methods:** cellular, Wi-Fi, Bluetooth — each carrying its own risk profile.

### Wireless security settings

| Element | Detail |
|---|---|
| Installation | Site surveys, heat maps — planning coverage and interference before deployment |
| Encryption | WPA3 |
| Authentication | AAA/RADIUS, cryptographic protocols, authentication protocols |

### Application security

| Technique | Purpose |
|---|---|
| Input validation | Rejects malformed/malicious input before processing |
| Secure cookies | Protects session tokens from theft/tampering |
| Static code analysis | Reviews source code for flaws without executing it |
| Code signing | Verifies software authenticity and integrity |
| Sandboxing | Runs untrusted code in an isolated environment |
| Monitoring | Ongoing visibility into application behavior |

### Asset management lifecycle

| Phase | Activities |
|---|---|
| Acquisition/procurement | Vetting and obtaining assets securely |
| Assignment/accounting | Ownership, classification |
| Monitoring/tracking | Inventory, enumeration |
| Disposal/decommissioning | Sanitization, destruction, certification of destruction, data retention compliance |

---

## 💻 Examples

```text
"A new server image is created, deployed to production, and periodically
checked against its original configuration."
→ Secure baseline: establish, deploy, maintain

"An employee uses their personal phone for work email under a formal
policy allowing it."
→ BYOD

"A wireless deployment maps signal strength across a building before
installing access points."
→ Site survey / heat map

"Source code is scanned for vulnerabilities before it's ever compiled or
run."
→ Static code analysis

"A decommissioned hard drive is physically destroyed and a certificate is
issued proving it."
→ Destruction + certification, as part of disposal/decommissioning
```

---

## 🚨 Common Mistakes

- Treating a secure baseline as a one-time setup instead of an ongoing establish/deploy/maintain cycle
- Confusing COPE (corporate-owned, but usable personally) with CYOD (employee picks from an approved list, but it's still typically corporate-owned) — subtle but testable distinction
- Skipping certification of destruction when disposing of sensitive media — proof of destruction matters for compliance, not just the act itself
- Assuming application security (input validation, code signing) is covered by infrastructure hardening — they're separate layers requiring separate attention

---

## 📖 Further Reading

- [CompTIA Security+ (SY0-701) official exam objectives](https://www.comptia.org/certifications/security)

---

## 🔗 Related Notes

- [[Vulnerability Management and Security Monitoring]]
- [[Hardening Devices and Systems for a Scenario]] — drill note
- [[Applying Asset Management Practices]] — drill note
- [[Security+ Codex]]
