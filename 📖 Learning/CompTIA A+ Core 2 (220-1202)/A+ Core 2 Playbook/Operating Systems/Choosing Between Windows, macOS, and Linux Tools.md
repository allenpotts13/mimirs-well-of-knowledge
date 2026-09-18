---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA A+
difficulty: Intermediate
tags:
  - a-plus-1202
  - macos
  - linux
aliases:
  - Cross-Platform Tool Choice
publish: true
permalink: comptia-a-core-2-220-1202/choosing-between-windows-macos-and-linux-tools
---

# <span class="rune">ᚲ</span> Choosing Between Windows, macOS, and Linux Tools

> *Same job, three different platforms, three different command vocabularies.*

---

## 🎯 Problem

A scenario names a platform (macOS or Linux) and a task. Identify the correct tool, command, or file type for that specific OS.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "back up a Mac automatically" | Time Machine |
| "encrypt an entire Mac's drive" | FileVault |
| "a downloaded Mac app needs mounting before install" | .dmg file |
| "quit an unresponsive Mac application" | Force Quit |
| "search for a file system-wide on a Mac" | Spotlight |
| "list files with permissions in Linux" | `ls -l` |
| "change a file's permissions in Linux" | `chmod` |
| "install a package on a Debian-based Linux distro" | `apt` |
| "install a package on a Fedora/RHEL-based distro" | `dnf` |
| "run a command as root for one command only, in Linux" | `sudo` |
| "check disk space usage in Linux" | `df` (filesystem-level) or `du` (directory/file-level) |

---

## 🧠 Why This Pattern Works

macOS tool names are mostly unique brand terms (Time Machine, FileVault, Spotlight) tied to specific Apple features — recognizing the *name* is the whole skill. Linux commands are short, POSIX-style verbs, and the two package managers (`apt` vs. `dnf`) map to distro family (Debian/Ubuntu vs. Fedora/RHEL) — the scenario naming a distro family is your cue for which one applies.

---

## 🚨 Common Mistakes

- Using `apt` syntax/thinking on a Fedora-based system where `dnf` is correct, or vice versa
- Confusing `su` (switch to another user, typically staying there) with `sudo` (elevate for a single command)
- Assuming FileVault and Time Machine solve the same problem — one is encryption, the other is backup, both can and should run together

---

## 💡 Wisdom from Mímir

When a Linux scenario doesn't name a specific distro, the *command family* (file management, package management, networking) still narrows it — you don't need to know every distro to know `chmod` changes permissions.

---

## 🔗 Related Notes

- [[macOS, Linux, and Application Management]] — full concept writeup
- [[A+ Core 2 Codex]]
