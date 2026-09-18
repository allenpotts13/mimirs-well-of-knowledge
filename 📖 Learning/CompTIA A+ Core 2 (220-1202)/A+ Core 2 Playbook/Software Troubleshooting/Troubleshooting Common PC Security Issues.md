---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA A+
difficulty: Intermediate
tags:
  - a-plus-1202
  - troubleshooting
  - security
aliases:
  - Troubleshooting PC Malware Symptoms
publish: true
permalink: comptia-a-core-2-220-1202/troubleshooting-common-pc-security-issues
---

# <span class="rune">ᚲ</span> Troubleshooting Common PC Security Issues

> *A fake antivirus pop-up is not your antivirus working — it's usually the opposite.*

---

## 🎯 Problem

A scenario describes suspicious desktop/browser behavior on a Windows PC. Diagnose the likely security cause.

---

## 🤔 Mental Model

| Symptom | Likely cause |
|---|---|
| Unable to access the network | Malware interfering with network settings/DNS |
| False alerts regarding antivirus protection | Rogue/fake security software (itself malware) |
| Desktop alerts, unwanted OS notifications | Adware or a compromised background process |
| Altered/missing/renamed files | Ransomware, unauthorized access, or corruption |
| OS update failures | Malware interference, corruption, or insufficient permissions |
| Random/frequent browser pop-ups | Adware or browser hijack |
| Certificate warnings | Invalid cert, expired cert, or an on-path attack intercepting traffic |
| Browser redirection | DNS hijack, malicious extension, or hosts file tampering |
| Degraded browser performance | Malicious extension or excessive background activity |

---

## 🧠 Why This Pattern Works

PC security symptoms split into **system-level** (files altered, network access blocked, false AV alerts) and **browser-level** (pop-ups, redirection, certificate warnings, degraded performance). Both categories point to the same underlying culprits — malware, adware, or a hijacked configuration — but the fix differs: system-level often needs the [[Identifying Malware Types and Removal Steps|10-step removal procedure]], while browser-level can sometimes be resolved by removing a malicious extension or resetting browser settings first.

---

## 🚨 Common Mistakes

- Trusting a pop-up claiming "your antivirus found a threat, click here" — this is a classic rogue-AV social engineering pattern, not a legitimate alert
- Treating a certificate warning as something to click through — it can indicate an on-path attack intercepting supposedly secure traffic
- Assuming missing files are always ransomware — could also be accidental deletion or a permissions/sync issue, so verify before assuming the worst

---

## 💡 Wisdom from Mímir

Any alert that creates urgency and demands you click *right now* — especially about your own antivirus — is a stronger malware signal than the thing it's warning you about.

---

## 🔗 Related Notes

- [[Software Troubleshooting]] — full concept writeup
- [[Identifying Malware Types and Removal Steps]] — drill note
- [[A+ Core 2 Codex]]
