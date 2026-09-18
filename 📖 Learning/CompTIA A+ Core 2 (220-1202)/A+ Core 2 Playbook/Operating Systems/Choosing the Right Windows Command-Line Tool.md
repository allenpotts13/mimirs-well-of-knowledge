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
  - Windows CLI Tool Choice
publish: true
permalink: comptia-a-core-2-220-1202/choosing-the-right-windows-command-line-tool
---

# <span class="rune">ᚲ</span> Choosing the Right Windows Command-Line Tool

> *`chkdsk` and `sfc` sound alike and get confused constantly — they check completely different layers.*

---

## 🎯 Problem

A scenario describes a task or diagnostic need. Identify the correct Windows command-line tool.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "verify a system's current IP configuration" | `ipconfig` |
| "test basic connectivity to a host" | `ping` |
| "trace the hop-by-hop path to a remote host" | `tracert` (or `pathping` for latency stats per hop) |
| "resolve a hostname to an IP or troubleshoot DNS" | `nslookup` |
| "view active network connections" | `netstat` |
| "check/repair file system errors on a drive" | `chkdsk` |
| "check/repair corrupted Windows system files" | `sfc` |
| "partition or reformat a disk from the command line" | `diskpart` / `format` |
| "immediately apply updated Group Policy" | `gpupdate` |
| "confirm which policies are currently applied to a machine" | `gpresult` |
| "robustly copy a large folder tree, resumable" | `robocopy` |
| "check what user is currently logged in" | `whoami` |

---

## 🧠 Why This Pattern Works

Group these by what they touch: **network** tools (`ipconfig`, `ping`, `tracert`, `pathping`, `nslookup`, `netstat`, `net use`) diagnose connectivity; **disk** tools (`chkdsk`, `format`, `diskpart`) manage storage; **file** tools (`md`, `rmdir`, `robocopy`) manage the file system; **OS management** tools (`gpupdate`, `gpresult`, `sfc`) manage policy and system integrity specifically. `chkdsk` vs `sfc` is the single most-tested pair — `chkdsk` is about the disk/filesystem, `sfc` is about Windows's own protected system files.

---

## 🚨 Common Mistakes

- Running `chkdsk` expecting it to fix corrupted Windows system files — that's `sfc /scannow`
- Confusing `tracert` (path only) with `pathping` (path + latency/loss statistics per hop)
- Forgetting `gpupdate` applies policy while `gpresult` only reports what's already applied — neither substitutes for the other

---

## 💡 Wisdom from Mímir

Ask "is this about the disk, the network, or Windows's own files?" — that question alone eliminates most of the wrong answers on any command-line tool question.

---

## 🔗 Related Notes

- [[Windows Administration Tools and Command Line]] — full concept writeup
- [[A+ Core 2 Codex]]
