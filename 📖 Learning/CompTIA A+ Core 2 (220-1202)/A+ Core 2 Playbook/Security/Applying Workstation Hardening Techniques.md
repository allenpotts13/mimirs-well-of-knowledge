---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA A+
difficulty: Intermediate
tags:
  - a-plus-1202
  - security
aliases:
  - Workstation Hardening Choice
publish: true
permalink: comptia-a-core-2-220-1202/applying-workstation-hardening-techniques
---

# <span class="rune">ᚲ</span> Applying Workstation Hardening Techniques

> *Nearly every hardening step here is "turn something off" or "lock something down" — the defaults are the vulnerability.*

---

## 🎯 Problem

A scenario describes a workstation security gap. Identify the correct hardening technique.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "inserting a USB drive automatically launches software" | Disable AutoRun |
| "an account with no password expiration and excessive access" | Apply account expiration, restrict permissions |
| "guest account is still enabled on a shared workstation" | Disable guest account |
| "brute-force login attempts aren't being stopped" | Enable failed-attempt lockout |
| "workstation left unlocked when a user steps away" | Screensaver lock / timeout screen lock |
| "sensitive data on a stolen laptop is fully readable" | Data-at-rest encryption |
| "default admin credentials were never changed" | Change default administrator account/password |
| "unnecessary background services increase attack surface" | Disable unused services |
| "firmware-level tampering is a concern" | BIOS/UEFI password |

---

## 🧠 Why This Pattern Works

Workstation hardening is fundamentally about reducing what's *possible* by default: fewer active accounts, fewer running services, fewer automatic behaviors (AutoRun), and more friction against unauthorized access (lockouts, timeouts, encryption). Nearly every correct answer either disables a convenience feature or adds a barrier — that pattern alone eliminates most wrong answers.

---

## 🚨 Common Mistakes

- Treating account lockout and screen timeout as solving the same problem — lockout stops brute-force login attempts, timeout stops an unattended unlocked session
- Forgetting BIOS/UEFI passwords protect firmware settings specifically, separate from the OS login entirely
- Overlooking that data-at-rest encryption (BitLocker/FileVault) is the actual fix for "stolen device, readable data," not just a login password

---

## 💡 Wisdom from Mímir

If a hardening scenario describes something that "just works by default," the answer is almost always to turn that default off.

---

## 🔗 Related Notes

- [[Workstation Hardening, Mobile Security, and Data Destruction]] — full concept writeup
- [[A+ Core 2 Codex]]
