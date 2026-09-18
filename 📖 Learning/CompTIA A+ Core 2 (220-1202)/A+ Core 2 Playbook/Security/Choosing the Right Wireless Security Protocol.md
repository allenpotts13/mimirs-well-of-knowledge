---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA A+
difficulty: Beginner
tags:
  - a-plus-1202
  - security
  - networking
aliases:
  - Wireless Security Protocol Choice
publish: true
permalink: comptia-a-core-2-220-1202/choosing-the-right-wireless-security-protocol
---

# <span class="rune">ᚲ</span> Choosing the Right Wireless Security Protocol

> *If WEP is an option on the exam, it is never the right answer.*

---

## 🎯 Problem

A scenario describes wireless security requirements or an authentication mechanism. Choose the correct protocol.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "strongest currently available Wi-Fi security" | WPA3 |
| "widely deployed, still secure, AES-based" | WPA2 |
| "older, deprecated encryption method to avoid" | TKIP (or WEP, if listed — always wrong) |
| "centralized authentication for network access (RADIUS-style)" | RADIUS or TACACS+ |
| "ticket-based authentication used by Active Directory" | Kerberos |
| "login requires more than one verification factor" | Multifactor authentication |

---

## 🧠 Why This Pattern Works

Wireless protocol questions almost always ask you to rank security strength: WPA3 > WPA2(AES) > WPA(TKIP) > WEP. Authentication protocol questions (RADIUS/TACACS+/Kerberos) are a separate axis entirely — these authenticate users/devices to a network or domain, independent of which wireless encryption standard is in use.

---

## 🚨 Common Mistakes

- Picking TKIP when AES is available in the same answer set — TKIP is the weaker legacy option
- Confusing WPA2 and WPA3 capability — WPA3 adds stronger encryption and protection against offline dictionary attacks
- Mixing up RADIUS (network access AAA) with Kerberos (domain authentication tickets) — similar purpose, different context

---

## 💡 Wisdom from Mímir

When ranking wireless security, newer always beats older: WPA3 beats WPA2 beats WPA beats WEP, with no exceptions the exam will test.

---

## 🔗 Related Notes

- [[Windows and Network Security Configuration]] — full concept writeup
- [[A+ Core 2 Codex]]
