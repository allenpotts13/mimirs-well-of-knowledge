---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA A+
difficulty: Beginner
tags:
  - a-plus-1201
  - hardware
  - printers
aliases:
  - Printer Connectivity Choice
publish: true
permalink: comptia-a-core-1-220-1201/choosing-a-multifunction-printer-connection-type
---

# <span class="rune">ᚲ</span> Choosing a Multifunction Printer Connection Type

> *One printer, three ways to reach it — and a security/sharing question hiding behind each one.*

---

## 🎯 Problem

A scenario describes how a printer needs to be shared, secured, or connected. Choose the connectivity method or configuration setting.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "single user, printer sits right next to the PC" | USB |
| "shared by an entire office over the existing network" | Ethernet (wired network printer) |
| "needs to be relocated without running new cable" | Wireless |
| "many users share one printer through a central queue" | Print server |
| "must confirm who printed what, for compliance" | Audit logs |
| "sensitive documents shouldn't sit in the output tray unattended" | Secured/pull printing |
| "scan a document straight to a user's email or a network folder" | Network scan services (email/SMB) |
| "restrict who can walk up and release a print job" | User authentication / badging |

---

## 🧠 Why This Pattern Works

Printer connectivity questions separate into *how it connects* (USB/Ethernet/wireless) and *how it's governed once shared* (print server, security, scan services). A single-user desk printer rarely needs a print server or audit logging; a shared departmental printer almost always does. The scenario's *audience size* (one user vs. a whole office) is the fastest signal for which half of this list applies.

---

## 🚨 Common Mistakes

- Defaulting to "wireless" for any shared printer — wired Ethernet is usually more reliable and still the standard for office deployments
- Confusing a print server (queues/manages jobs for many users) with simply sharing a printer from one user's PC (informal, fragile, ties up that PC)
- Forgetting scan-to-email/SMB/cloud is a *scanner* feature riding on the same multifunction device, not a print feature

---

## 💡 Wisdom from Mímir

If a printer scenario mentions compliance, sensitive documents, or "who printed this," the answer lives in the security section (authentication, badging, audit logs, secured prints) — not the connectivity section.

---

## 🔗 Related Notes

- [[System Assembly, Power, and Printers]] — full concept writeup
- [[Troubleshooting Printer Issues]] — drill note
- [[A+ Core 1 Codex]]
