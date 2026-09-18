---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA A+
difficulty: Intermediate
tags:
  - a-plus-1202
  - windows
aliases:
  - Windows MMC Snap-In Choice
publish: true
permalink: comptia-a-core-2-220-1202/choosing-the-right-windows-administrative-tool
---

# <span class="rune">ᚲ</span> Choosing the Right Windows Administrative Tool

> *Every `.msc` and `.exe` on this list has exactly one job. Name the job, name the tool.*

---

## 🎯 Problem

A scenario describes an administrative task in Windows. Identify the correct GUI tool.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "review logs of a crash or security event" | Event Viewer (`eventvwr.msc`) |
| "resize a partition or assign a drive letter" | Disk Management (`diskmgmt.msc`) |
| "automate a task to run every night" | Task Scheduler (`taskschd.msc`) |
| "a driver needs to be updated or a device is disabled" | Device Manager (`devmgmt.msc`) |
| "add or modify a local user account" | Local Users and Groups (`lusrmgr.msc`) |
| "track detailed resource usage trends over time" | Performance Monitor (`perfmon.msc`) |
| "check what's slowing down boot time" | System Configuration (`msconfig.exe`) or Task Manager → Startup |
| "gather full hardware/software inventory of a machine" | System Information (`msinfo32.exe`) |
| "free up disk space" | Disk Cleanup (`cleanmgr.exe`) |
| "enforce a local security/policy setting on a standalone PC" | Local Group Policy Editor (`gpedit.msc`) |

---

## 🧠 Why This Pattern Works

Windows administrative tools are named for exactly what they inspect or manage — the trick is knowing the `.msc`/`.exe` filename behind the friendly name, since scenarios sometimes name the tool by function and sometimes by filename. Group them mentally: **hardware** (Device Manager, Disk Management), **monitoring** (Event Viewer, Performance Monitor, Resource Monitor, Task Manager), **accounts/policy** (Local Users and Groups, Group Policy Editor), and **system state** (System Configuration, System Information, Disk Cleanup).

---

## 🚨 Common Mistakes

- Reaching for Task Manager for deep historical trend data — that's Performance Monitor's job, Task Manager is real-time only
- Confusing Disk Management (partitions/volumes) with Disk Cleanup (freeing space) or Disk Defragment (optimizing an existing drive)
- Using Device Manager when the scenario is really about *policy* enforcement — that's Group Policy Editor

---

## 💡 Wisdom from Mímir

If the scenario says "over time" or "historical," think Performance Monitor or Event Viewer. If it says "right now," think Task Manager or Resource Monitor.

---

## 🔗 Related Notes

- [[Windows Administration Tools and Command Line]] — full concept writeup
- [[A+ Core 2 Codex]]
