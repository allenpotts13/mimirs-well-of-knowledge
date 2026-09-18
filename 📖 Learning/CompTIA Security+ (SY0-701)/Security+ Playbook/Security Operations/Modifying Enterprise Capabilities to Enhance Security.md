---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Security+
difficulty: Intermediate
tags:
  - security-plus
  - security-operations
aliases:
  - Enterprise Security Capability Choice
publish: true
permalink: comptia-security-sy0-701/modifying-enterprise-capabilities-to-enhance-security
---

# <span class="rune">ᚲ</span> Modifying Enterprise Capabilities to Enhance Security

> *SPF, DKIM, and DMARC all fight email spoofing — but only one of them tells the receiving server what to actually do about a failure.*

---

## 🎯 Problem

A scenario describes an enterprise security gap. Choose the correct capability or setting to enhance.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "emails from a domain are being spoofed and there's no policy for handling failures" | DMARC |
| "need to verify which servers are authorized to send mail for a domain" | SPF |
| "need to cryptographically verify message content wasn't altered" | DKIM |
| "block users from accessing malicious domains by name before a connection is made" | DNS filtering |
| "restrict web access by content category rather than individual URLs" | Content filtering |
| "restrict access to a specific list of known-bad URLs" | URL/block-list filtering |
| "detect and block traffic matching known attack signatures" | IPS (signature-based) |
| "monitor for new/emerging attack trends beyond known signatures" | IDS/IPS (trend-based) |
| "critical system files need to be verified as unaltered" | File integrity monitoring |
| "the organization needs Windows-specific centralized policy enforcement" | Group Policy |
| "a Linux system needs mandatory access control enforced at the kernel level" | SELinux |

---

## 🧠 Why This Pattern Works

Enterprise capability questions test whether you know each tool's *specific* job among several that all sound similar. The SPF/DKIM/DMARC trio is the most commonly confused: SPF authorizes senders, DKIM verifies message integrity, and DMARC is the policy layer that tells receiving servers what to do when SPF or DKIM fails (and where to report it). Web/DNS/content/URL filtering differ by *what's being filtered on* — domain name, category, or specific address.

---

## 🚨 Common Mistakes

- Recommending SPF alone to stop spoofed email — without DMARC, there's no enforcement policy for what happens when SPF fails
- Confusing DNS filtering (blocks by domain name resolution) with content filtering (blocks by category) or URL filtering (blocks by specific address)
- Treating Group Policy and SELinux as interchangeable — one is Windows-specific, the other Linux-specific, and they operate differently
- Assuming any IDS/IPS handles both known-signature and emerging-trend detection equally well — trend/anomaly detection and signature matching are different capabilities

---

## 💡 Wisdom from Mímir

For email security specifically, remember: SPF says who can send, DKIM proves what was sent, DMARC decides what happens when either check fails.

---

## 🔗 Related Notes

- [[Enterprise Security Capabilities and Identity Access Management]] — full concept writeup
- [[Security+ Codex]]
