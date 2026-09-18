---
type: concept
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA A+
difficulty: Intermediate
tags:
  - a-plus-1202
  - security
aliases:
  - A+ Workstation Hardening
  - A+ Data Destruction
publish: true
permalink: comptia-a-core-2-220-1202/workstation-hardening-mobile-security-and-data-destruction
---

# <span class="rune">ᛟ</span> Workstation Hardening, Mobile Security, and Data Destruction

> *Malware removal has a required order of operations. Data destruction has a required level of certainty. Neither forgives skipped steps.*

---

## 🎯 Purpose

Covers 2.6-2.9 of the Security domain: SOHO malware removal procedure, workstation hardening techniques, mobile device security methods, and data destruction/disposal methods.

---

## 🧠 Key Ideas

- CompTIA defines a specific **10-step SOHO malware removal procedure**, in order — this is memorized, not improvised
- Workstation hardening is a checklist of small defaults to change: passwords, BIOS/UEFI passwords, account restrictions, disabling unused services/AutoRun
- Mobile device security layers hardening (encryption, screen locks), patch management, endpoint security, and MDM policy together
- Data destruction methods split into **physical destruction** (drilling, shredding, degaussing, incineration — irreversible) and **wiping/formatting** (software-level, verifiable via certificate of destruction when outsourced)
- Password policy has five independent knobs: length, character types, uniqueness, complexity, expiration — a scenario can test any one in isolation

---

## ⚙️ How It Works

### The SOHO malware removal procedure (memorize the order)

1. Investigate and verify malware symptoms.
2. Quarantine the infected system.
3. Disable System Restore (Windows Home).
4. Remediate infected systems.
5. Update anti-malware software.
6. Scan and removal techniques (safe mode, preinstallation environment).
7. Reimage/reinstall if necessary.
8. Schedule scans and run updates.
9. Enable System Restore and create a restore point (Windows Home).
10. Educate the end user.

> [!tip] Why disable System Restore mid-process
> If System Restore stays active during removal, it can inadvertently save/restore an infected state. It's disabled early, then deliberately re-enabled with a clean restore point near the end.

### Workstation hardening checklist

| Category | Actions |
|---|---|
| Passwords | Data-at-rest encryption, BIOS/UEFI passwords, change default admin credentials |
| Accounts | Restrict permissions, restrict login times, disable guest account, failed-attempt lockout, timeout/screen lock, account expiration |
| System | Disable AutoRun, disable unused services |
| End-user practices | Screensaver locks, log off when not in use, secure PII/passwords, use password managers, physically secure hardware |

### Mobile device security

| Layer | Options |
|---|---|
| Hardening | Device encryption, screen locks (PIN, pattern, swipe, biometric), configuration profiles |
| Patch management | OS updates, application updates |
| Endpoint security | Antivirus, anti-malware, content filtering |
| Loss/theft response | Locator apps, remote wipe, remote backup |
| Policy | MDM, BYOD vs. corporate-owned, profile security requirements, failed login restrictions |

### Data destruction and disposal

| Method | Type | Reversible? |
|---|---|---|
| Drilling, shredding, incineration | Physical destruction | No |
| Degaussing | Physical (magnetic erasure) | No |
| Low-level format / wiping | Software | Effectively no, if done properly |
| Standard format | Software | Data often recoverable — not true destruction |
| Outsourcing to a third party | — | Requires certificate of destruction/recycling |

---

## 💻 Examples

```text
"A workstation is confirmed infected with ransomware and needs to be
handled per procedure."
→ Investigate/verify → quarantine → disable System Restore → remediate...
(follow the 10-step order)

"A company decommissions drives containing regulated financial data and
needs proof of destruction for compliance."
→ Physical destruction (or certified wipe) with a certificate of
destruction from the vendor

"A lost company phone needs its corporate data removed immediately."
→ Remote wipe

"A password policy requires it change automatically after 90 days."
→ Expiration

"A drive is 'quick formatted' before disposal and the company assumes the
data is gone."
→ Incorrect assumption — standard/quick format does not securely erase data
"
```

---

## 🚨 Common Mistakes

- Skipping or reordering the malware removal steps — most commonly, forgetting to disable System Restore before remediation, or forgetting to re-enable it afterward
- Treating a standard format as secure data destruction — it isn't; low-level wiping or physical destruction is required for sensitive data
- Confusing password complexity (character variety) with password length — both are separate, independently testable settings
- Assuming MDM only applies to corporate-owned devices — BYOD devices are also managed, just under different policy terms

---

## 📖 Further Reading

- [CompTIA A+ Core 2 (220-1202) official exam objectives](https://www.comptia.org/certifications/a)

---

## 🔗 Related Notes

- [[Security Fundamentals, Malware, and Social Engineering]]
- [[Identifying Malware Types and Removal Steps]] — drill note
- [[Applying Workstation Hardening Techniques]] — drill note
- [[Securing Mobile Devices]] — drill note
- [[A+ Core 2 Codex]]
