---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Security+
difficulty: Beginner
tags:
  - security-plus
  - security-operations
aliases:
  - Asset Management Choice
publish: true
permalink: comptia-security-sy0-701/applying-asset-management-practices
---

# <span class="rune">ᚲ</span> Applying Asset Management Practices

> *Disposal isn't done until you can prove it happened — that's the exam's whole point about certification of destruction.*

---

## 🎯 Problem

A scenario describes an asset lifecycle event. Identify the correct asset management activity.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "a new device is vetted and purchased through a formal process" | Acquisition/procurement |
| "a device is tagged and assigned to a specific employee" | Assignment/accounting |
| "an inventory system tracks every known device on the network" | Monitoring/asset tracking (inventory, enumeration) |
| "an old server is wiped before being resold" | Sanitization |
| "an old drive is physically shredded" | Destruction |
| "the company needs formal proof that data was properly destroyed" | Certification of destruction |
| "data must be kept for a legally defined period even after a device is retired" | Data retention |
| "a device is found on the network that isn't in the inventory system" | Enumeration reveals unmanaged/shadow asset |

---

## 🧠 Why This Pattern Works

Asset management follows a lifecycle: acquire → assign/track → dispose. Each phase has a distinct security concern — acquisition is about vetting sources, tracking is about knowing what exists (an untracked asset is an unmanaged risk), and disposal is about ensuring data doesn't leak after the device leaves the organization's control. Certification of destruction specifically exists because "we destroyed it" isn't sufficient without documented proof for compliance purposes.

---

## 🚨 Common Mistakes

- Treating sanitization (data wiped, hardware reusable) and destruction (hardware physically unusable) as the same disposal method
- Skipping certification of destruction when a compliance framework requires documented proof
- Assuming inventory tracking is a one-time task rather than continuous monitoring/enumeration
- Forgetting data retention requirements can outlive the hardware itself — retention is about the data, not the device

---

## 💡 Wisdom from Mímir

An asset's security risk doesn't end when it's retired — it ends when its data is verifiably destroyed or sanitized, with proof to show for it.

---

## 🔗 Related Notes

- [[Hardening and Asset Management]] — full concept writeup
- [[Security+ Codex]]
