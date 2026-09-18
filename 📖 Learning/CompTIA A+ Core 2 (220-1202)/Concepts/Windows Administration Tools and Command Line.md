---
type: concept
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA A+
difficulty: Intermediate
tags:
  - a-plus-1202
  - windows
aliases:
  - A+ Windows Tools
  - A+ Command Line Tools
publish: true
permalink: comptia-a-core-2-220-1202/windows-administration-tools-and-command-line
---

# <span class="rune">ᛟ</span> Windows Administration Tools and Command Line

> *Every MMC snap-in and command-line tool on this exam has exactly one job — the difficulty is remembering which.*

---

## 🎯 Purpose

Covers 1.4-1.5 of the Operating Systems domain: Windows GUI administrative tools (Task Manager, MMC snap-ins, and utilities) and command-line tools for navigation, networking, disk, and file management.

---

## 🧠 Key Ideas

- **Task Manager** tabs (Services, Startup, Performance, Processes, Users) each answer a different "what's happening right now" question
- **MMC snap-ins** are launched by their `.msc` filename — memorize name-to-purpose pairs (`eventvwr.msc` = Event Viewer, `diskmgmt.msc` = Disk Management, etc.)
- Command-line tools split into clear families: navigation, network, disk management, file management, and OS management
- `sfc` (System File Checker) repairs corrupted system files; it is not a disk repair tool — that's `chkdsk`
- Group Policy tools (`gpupdate`, `gpresult`) push and verify policy separately from where policy is edited (`gpedit.msc`)

---

## ⚙️ How It Works

### Task Manager and MMC snap-ins

| Tool | Purpose |
|---|---|
| Task Manager → Processes/Performance | Live resource usage per app/system-wide |
| Task Manager → Startup | Enable/disable apps that launch at boot |
| Task Manager → Services | Start/stop/inspect Windows services |
| `eventvwr.msc` | Event Viewer — logs of system/app/security events |
| `diskmgmt.msc` | Disk Management — partitions, volumes, drive letters |
| `taskschd.msc` | Task Scheduler — automate tasks to run on a schedule/trigger |
| `devmgmt.msc` | Device Manager — hardware devices, drivers |
| `certmgr.msc` | Certificate Manager |
| `lusrmgr.msc` | Local Users and Groups |
| `perfmon.msc` | Performance Monitor — detailed resource tracking over time |
| `gpedit.msc` | Local Group Policy Editor |
| `regedit.exe` | Registry Editor |
| `msinfo32.exe` | System Information |
| `resmon.exe` | Resource Monitor — deeper real-time resource view than Task Manager |
| `msconfig.exe` | System Configuration — boot options, startup, services |
| `cleanmgr.exe` | Disk Cleanup |
| `dfrgui.exe` | Disk Defragment |

### Command-line tools by family

| Family | Tools |
|---|---|
| Navigation | `cd`, `dir` |
| Network | `ipconfig`, `ping`, `netstat`, `nslookup`, `net use`, `tracert`, `pathping` |
| Disk management | `chkdsk`, `format`, `diskpart` |
| File management | `md`, `rmdir`, `robocopy` |
| OS management | `gpupdate`, `gpresult`, `sfc` |
| Informational | `hostname`, `net user`, `winver`, `whoami`, `[command] /?` |

---

## 💻 Examples

```text
"A technician needs to see which processes are consuming the most CPU
right now."
→ Task Manager → Processes (or Performance tab)

"A technician suspects corrupted Windows system files after a crash."
→ sfc /scannow

"A technician needs to check a drive for file-system errors."
→ chkdsk

"A technician needs to force a computer to immediately re-apply Group
Policy without waiting for the refresh interval."
→ gpupdate

"A technician wants to trace the network path and latency to a remote
host, hop by hop."
→ tracert or pathping

"A technician needs to copy a large folder structure robustly, resuming
if interrupted."
→ robocopy
```

---

## 🚨 Common Mistakes

- Confusing `chkdsk` (disk/filesystem errors) with `sfc` (corrupted Windows system files) — they check different things entirely
- Using `gpedit.msc` when the scenario actually needs `gpupdate` (editing policy vs. applying/refreshing it)
- Forgetting Resource Monitor (`resmon.exe`) gives more granular data than Task Manager, useful when Task Manager's view isn't detailed enough
- Mixing up `netstat` (shows active connections) with `nslookup` (queries DNS) when a scenario is vague about "checking the network"

---

## 📖 Further Reading

- [CompTIA A+ Core 2 (220-1202) official exam objectives](https://www.comptia.org/certifications/a)

---

## 🔗 Related Notes

- [[Operating System Types and Installation]]
- [[Windows Settings and Networking Configuration]]
- [[Choosing the Right Windows Administrative Tool]] — drill note
- [[Choosing the Right Windows Command-Line Tool]] — drill note
- [[A+ Core 2 Codex]]
