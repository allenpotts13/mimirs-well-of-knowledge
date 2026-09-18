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
  - windows
aliases:
  - Troubleshooting Windows OS
publish: true
permalink: comptia-a-core-2-220-1202/troubleshooting-common-windows-os-issues
---

# <span class="rune">ᚲ</span> Troubleshooting Common Windows OS Issues

> *A BSOD is Windows telling you exactly what failed — if you know where to read the message.*

---

## 🎯 Problem

A scenario describes a Windows OS symptom. Diagnose the likely cause and the tool to confirm it.

---

## 🤔 Mental Model

| Symptom | Likely cause | Diagnostic tool |
|---|---|---|
| BSOD | Driver fault, failing hardware, corrupted system file | Event Viewer, minidump analysis |
| No OS found / boot issues | Corrupted boot files, wrong boot order, failing drive | BIOS boot order, repair install |
| Frequent shutdowns | Overheating, power issue, driver fault | Check temps, Event Viewer |
| Degraded performance, low memory warnings | Resource exhaustion, too many startup apps | Task Manager, Resource Monitor |
| Services not starting | Dependency failure, corrupted service, permissions | Services console, Event Viewer |
| Applications crashing | Corrupted install, missing dependency, resource conflict | Reinstall, check Event Viewer |
| USB controller resource warnings | Too many devices/hubs on limited resources | Device Manager |
| System instability | Multiple possible causes — isolate via safe mode | Boot into Safe Mode to narrow scope |
| Slow profile load | Corrupted or bloated user profile | Create/test with a new local profile |
| Time drift | CMOS battery dying, or NTP sync failure | Check BIOS time, `w32tm` sync |

---

## 🧠 Why This Pattern Works

Windows OS troubleshooting follows the shared methodology: isolate whether the issue is hardware-adjacent (BSOD, shutdowns, USB resource warnings — check Device Manager/Event Viewer) or software/resource-adjacent (performance, crashes, services — check Task Manager/Resource Monitor/Services). Safe Mode is the universal isolation tool — if a symptom disappears in Safe Mode, a third-party driver or startup app is implicated, not core Windows.

---

## 🚨 Common Mistakes

- Reinstalling the OS as a first response instead of checking Event Viewer for a specific, fixable cause
- Assuming every BSOD means failing hardware — a bad driver is far more common and far cheaper to fix
- Overlooking a dying CMOS battery as the cause of "random" time drift, treating it as a software/NTP issue only

---

## 💡 Wisdom from Mímir

Event Viewer is the first stop for almost every "why did this happen" Windows question — it usually already logged the answer.

---

## 🔗 Related Notes

- [[Software Troubleshooting]] — full concept writeup
- [[Windows Administration Tools and Command Line]] — full concept writeup
- [[A+ Core 2 Codex]]
