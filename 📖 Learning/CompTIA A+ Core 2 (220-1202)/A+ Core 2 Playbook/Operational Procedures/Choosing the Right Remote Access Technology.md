---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA A+
difficulty: Beginner
tags:
  - a-plus-1202
  - operational-procedures
  - networking
aliases:
  - Remote Access Tool Choice
publish: true
permalink: comptia-a-core-2-220-1202/choosing-the-right-remote-access-technology
---

# <span class="rune">ᚲ</span> Choosing the Right Remote Access Technology

> *Full desktop, command line, or fleet-wide — the scope of control needed picks the tool.*

---

## 🎯 Problem

A scenario describes a remote support or management need. Choose the correct remote access technology.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "full graphical remote control of a Windows desktop" | RDP |
| "cross-platform graphical remote control (not just Windows)" | VNC |
| "secure remote command-line session" | SSH |
| "secure tunnel into a private corporate network" | VPN |
| "manage and monitor hundreds of endpoints centrally" | RMM |
| "run remote PowerShell/management commands against Windows machines" | WinRM |
| "walk a non-technical user through a fix visually, ad hoc" | Screen-sharing software |
| "hold a support conversation with video" | Videoconferencing software |
| "move files to/from a remote machine without full desktop control" | File transfer software |

---

## 🧠 Why This Pattern Works

Remote access tools separate by **scope** (single machine vs. fleet-wide) and **interface** (full GUI vs. command line vs. file-only). RDP and VNC both give full graphical control but differ by platform reach; SSH trades the GUI for a secure command line; RMM exists specifically for managing many machines at once, which none of the single-session tools are designed for. A scenario naming "many machines" or "a whole fleet" is the strongest signal to reach for RMM over anything else on this list.

---

## 🚨 Common Mistakes

- Picking RDP for a cross-platform (non-Windows) scenario — VNC is the platform-agnostic choice
- Using a single-session tool (RDP/VNC/SSH) for a scenario that clearly describes managing many endpoints — that calls for RMM
- Forgetting each remote access method carries its own security considerations (e.g., RDP exposed directly to the internet is a common attack vector) — the exam expects awareness that convenience isn't free

---

## 💡 Wisdom from Mímir

Count how many machines the scenario is actually about. One machine, interactively → RDP/VNC/SSH. Many machines, centrally → RMM.

---

## 🔗 Related Notes

- [[Safety, Compliance, Professionalism, and Emerging Technology]] — full concept writeup
- [[A+ Core 2 Codex]]
