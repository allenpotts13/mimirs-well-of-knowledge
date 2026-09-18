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
  - Device Hardening Choice
publish: true
permalink: comptia-security-sy0-701/hardening-devices-and-systems-for-a-scenario
---

# <span class="rune">ᚲ</span> Hardening Devices and Systems for a Scenario

> *BYOD, COPE, and CYOD sound like the same acronym family until you ask "who actually owns this device?"*

---

## 🎯 Problem

A scenario describes a device that needs securing, or a mobile deployment model. Identify the correct hardening technique or deployment model.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "employee's personal device, enrolled under policy" | BYOD |
| "company-owned device, but employee may also use it personally" | COPE |
| "employee picks from a company-approved list, device is still corporate property" | CYOD |
| "wireless deployment needs coverage mapped before installation" | Site survey / heat map |
| "wireless network needs the strongest current encryption" | WPA3 |
| "application needs to reject malformed input before processing it" | Input validation |
| "software's authenticity must be verifiable before installation" | Code signing |
| "untrusted code needs to run without risking the host system" | Sandboxing |
| "a specialized industrial control system can't be conventionally patched" | ICS/SCADA hardening considerations |
| "baseline configuration needs to be established, then kept current" | Secure baseline (establish → deploy → maintain) |

---

## 🧠 Why This Pattern Works

Mobile deployment models differ by **ownership** and **control**: BYOD is employee-owned with policy attached, COPE is company-owned but personally usable, CYOD is company-owned from an approved list (splitting the difference on user choice vs. control). Hardening more broadly is about reducing what's exposed on any given target — the *target type* (mobile, workstation, network device, cloud, ICS/embedded) determines which specific technique applies, but the underlying goal (minimize attack surface) is constant.

---

## 🚨 Common Mistakes

- Confusing COPE (still corporate-owned) with BYOD (employee-owned) — ownership is the deciding factor, not usage flexibility
- Assuming a secure baseline is a one-time setup rather than an ongoing establish/deploy/maintain cycle
- Treating sandboxing and input validation as solving the same problem — one isolates untrusted execution, the other filters untrusted input before it's even processed
- Forgetting ICS/SCADA and embedded systems often *can't* be patched conventionally, requiring compensating controls instead

---

## 💡 Wisdom from Mímir

For mobile deployment models, ask "who owns it, and how much can the employee personalize it?" That answers BYOD/COPE/CYOD every time.

---

## 🔗 Related Notes

- [[Hardening and Asset Management]] — full concept writeup
- [[Security+ Codex]]
