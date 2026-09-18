---
type: concept
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Security+
difficulty: Intermediate
tags:
  - security-plus
  - threats-vulnerabilities
aliases:
  - Security+ Attack Indicators
  - Security+ Mitigation Techniques
publish: true
permalink: comptia-security-sy0-701/attack-indicators-and-mitigation-techniques
---

# <span class="rune">ᛟ</span> Attack Indicators and Mitigation Techniques

> *This is the biggest single sub-objective in the whole certification — five attack categories and a dozen indicators, all in one "given a scenario" bucket.*

---

## 🎯 Purpose

Covers 2.4-2.5 of the Threats, Vulnerabilities, and Mitigations domain: analyzing indicators of malicious activity (a "given a scenario" objective, and the densest single sub-objective on the exam), and the mitigation techniques used to secure the enterprise.

---

## 🧠 Key Ideas

- Malicious activity spans five attack families: **malware**, **physical**, **network**, **application**, and **cryptographic/password** attacks — each with its own vocabulary
- **Indicators** (account lockout, impossible travel, concurrent session usage, resource consumption, missing logs) are the *symptoms* that reveal an attack is happening, separate from naming the attack itself
- **Amplified vs. reflected** DDoS both abuse third-party servers to attack a victim, but differ in mechanism: reflection spoofs the victim's address to redirect responses, amplification specifically exploits protocols that return a much larger response than the request
- Mitigation techniques form a layered defense: **segmentation/isolation** (limit blast radius), **access control/least privilege** (limit who can act), **hardening** (reduce what's exposed), and **monitoring/patching** (catch and close gaps over time)

---

## ⚙️ How It Works

### Malware and physical attacks

| Category | Examples |
|---|---|
| Malware | Ransomware, Trojan, worm, spyware, bloatware, virus, keylogger, logic bomb, rootkit |
| Physical | Brute force (physical access), RFID cloning, environmental attacks |

### Network, application, cryptographic, and password attacks

| Category | Examples |
|---|---|
| Network | DDoS (amplified/reflected), DNS attacks, wireless attacks, on-path, credential replay, malicious code |
| Application | Injection, buffer overflow, replay, privilege escalation, forgery, directory traversal |
| Cryptographic | Downgrade, collision, birthday attack |
| Password | Spraying (few passwords across many accounts), brute force (many passwords, one account) |

### Indicators of malicious activity

| Indicator | What it suggests |
|---|---|
| Account lockout | Possible brute-force/credential attack |
| Concurrent session usage | Possible credential sharing/compromise |
| Impossible travel | Login from geographically impossible locations in a short window |
| Resource consumption / inaccessibility | Possible DoS or cryptomining malware |
| Blocked content | A control triggered — worth investigating why |
| Out-of-cycle logging / missing logs | Possible tampering to hide activity |

### Mitigation techniques

| Technique | Purpose |
|---|---|
| Segmentation / isolation | Limits how far an incident can spread |
| Access control (ACLs, permissions), least privilege | Limits who/what can act on a resource |
| Application allow list | Only pre-approved software may run |
| Patching | Closes known vulnerabilities |
| Encryption | Protects data even if accessed |
| Monitoring | Detects issues in progress |
| Configuration enforcement | Keeps systems at a known-good baseline |
| Decommissioning | Removes systems that are no longer needed/supportable |
| Hardening techniques | Disable unused ports/protocols, change default passwords, remove unnecessary software, install endpoint protection/HIPS/host-based firewall |

---

## 💻 Examples

```text
"A user's account shows a login from New York and, eight minutes later, a
login from Singapore."
→ Impossible travel

"An attacker sends a small DNS query that triggers a much larger response
directed at the victim."
→ Amplified DDoS

"An attacker tries one common password against thousands of accounts to
avoid lockout thresholds."
→ Password spraying

"A web form fails to validate input, letting an attacker inject SQL
commands."
→ Injection attack (SQLi)

"A hardened server has all unused services disabled and default
credentials changed before deployment."
→ Hardening technique — reduces attack surface proactively
```

---

## 🚨 Common Mistakes

- Confusing password spraying (few passwords, many accounts, avoids lockout) with brute force (many passwords, one account, triggers lockout)
- Mixing up amplified DDoS (exploits response-size disparity) with reflected DDoS (exploits spoofed source addresses) — related but mechanically distinct
- Treating an indicator (like impossible travel) as the attack itself rather than evidence pointing toward one
- Assuming one mitigation technique is sufficient — real defense is layered (segmentation + least privilege + patching + monitoring together)

---

## 📖 Further Reading

- [CompTIA Security+ (SY0-701) official exam objectives](https://www.comptia.org/certifications/security)

---

## 🔗 Related Notes

- [[Identifying Vulnerability Types]]
- [[Identifying Malware and Physical Attack Types]] — drill note
- [[Identifying Network, Application, and Password Attacks]] — drill note
- [[Choosing the Right Mitigation Technique]] — drill note
- [[Security+ Codex]]
