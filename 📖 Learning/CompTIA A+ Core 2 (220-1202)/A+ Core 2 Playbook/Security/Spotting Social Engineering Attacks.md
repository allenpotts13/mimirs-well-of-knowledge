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
aliases:
  - Social Engineering Identification
publish: true
permalink: comptia-a-core-2-220-1202/spotting-social-engineering-attacks
---

# <span class="rune">ᚲ</span> Spotting Social Engineering Attacks

> *Same trick, different channel — phishing's variants are named entirely by delivery method and target.*

---

## 🎯 Problem

A scenario describes an attempt to manipulate a person rather than a system. Identify the specific social engineering technique.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "fraudulent email asking for credentials" | Phishing |
| "fraudulent phone call" | Vishing |
| "fraudulent text message" | Smishing |
| "malicious QR code" | QR code phishing |
| "highly targeted email at one specific person" | Spear phishing |
| "targeted at an executive specifically" | Whaling |
| "watching someone type their password" | Shoulder surfing |
| "following an employee through a secured door" | Tailgating |
| "pretending to be IT support or a vendor" | Impersonation |
| "searching thrown-out documents for sensitive info" | Dumpster diving |
| "fraudulent email requesting a wire transfer, appearing to be from an executive" | Business email compromise (BEC) |

---

## 🧠 Why This Pattern Works

All phishing variants share the same goal (trick someone into giving up credentials/money/access) but are named for *how* the message arrives (email/voice/SMS/QR) or *who* it targets (a specific person = spear phishing, an executive = whaling). Non-phishing social engineering (shoulder surfing, tailgating, impersonation, dumpster diving) is about physical-world manipulation rather than a message at all — recognizing "no message was sent" is the tell for this second group.

---

## 🚨 Common Mistakes

- Calling every social engineering attack "phishing" regardless of channel — the exam expects the specific term (vishing/smishing/QR phishing)
- Confusing spear phishing (targeted at a specific person) with generic phishing (mass, untargeted)
- Missing tailgating as a *physical* attack rather than assuming all social engineering happens over email/phone

---

## 💡 Wisdom from Mímir

Ask "how did the attacker reach the victim?" — a channel (email/call/text/QR) means it's a phishing variant; a physical presence means it's tailgating, shoulder surfing, impersonation, or dumpster diving.

---

## 🔗 Related Notes

- [[Security Fundamentals, Malware, and Social Engineering]] — full concept writeup
- [[A+ Core 2 Codex]]
