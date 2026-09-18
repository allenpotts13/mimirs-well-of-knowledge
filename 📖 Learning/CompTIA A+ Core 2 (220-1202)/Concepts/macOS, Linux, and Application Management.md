---
type: concept
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA A+
difficulty: Beginner
tags:
  - a-plus-1202
  - macos
  - linux
aliases:
  - A+ macOS and Linux
  - A+ Application Installation
publish: true
permalink: comptia-a-core-2-220-1202/macos-linux-and-application-management
---

# <span class="rune">ᛟ</span> macOS, Linux, and Application Management

> *Three different platforms, three different vocabularies — and the exam expects fluency in all three.*

---

## 🎯 Purpose

Covers 1.8-1.11 of the Operating Systems domain: macOS features/tools, Linux commands/files, application installation requirements, and cloud-based productivity tool setup.

---

## 🧠 Key Ideas

- macOS best practices (backups via **Time Machine**, antivirus, timely updates) mirror Windows best practices with different tool names
- macOS file types matter: **.dmg** (disk image, mount and drag to install), **.pkg** (installer package), **.app** (the application bundle itself)
- Linux commands split cleanly into file management, filesystem management, administrative, package management, network, and informational — each a small, memorizable list
- Application installation depends on **system requirements** (CPU/RAM/storage/VRAM, 32-bit vs. 64-bit) and **distribution method** (physical media, ISO, downloadable package, image deployment)
- Cloud productivity tools add a layer on top of local app management: identity sync, licensing assignment, and storage sync settings

---

## ⚙️ How It Works

### macOS tools and features

| Tool/Feature | Purpose |
|---|---|
| Time Machine | Built-in backup |
| FileVault | Full-disk encryption |
| Disk Utility | Partition/format/repair drives |
| Terminal | Command-line access |
| Keychain | Stored passwords/credentials |
| Spotlight | System-wide search |
| Mission Control / Multiple desktops | Window/workspace management |
| Force Quit | Kill an unresponsive app |
| iCloud | Sync — Drive, iMessage, FaceTime |

**System folders:** `/Applications`, `/Users`, `/Library`, `/System`, `/Users/Library`.

**File types:** `.dmg` (mountable disk image), `.pkg` (installer package), `.app` (application bundle).

### Linux commands by category

| Category | Commands |
|---|---|
| File management | `ls`, `pwd`, `mv`, `cp`, `rm`, `chmod`, `chown`, `grep`, `find` |
| Filesystem management | `fsck`, `mount` |
| Administrative | `su`, `sudo` |
| Package management | `apt`, `dnf` |
| Network | `ip`, `ping`, `curl`, `dig`, `traceroute` |
| Informational | `man`, `cat`, `top`, `ps`, `du`, `df` |
| Text editors | `nano` |

**Key config files:** `/etc/passwd` (user accounts), `/etc/shadow` (password hashes), `/etc/hosts` (static name resolution), `/etc/fstab` (filesystem mounts), `/etc/resolv.conf` (DNS config). **OS components:** `systemd` (init/service manager), kernel, bootloader. Root is the Linux superuser account.

### Application installation requirements

| Consideration | Detail |
|---|---|
| System requirements | CPU, RAM, storage, VRAM, 32-bit vs. 64-bit |
| Distribution method | Physical media, mountable ISO, downloadable package, image deployment |
| Impact | Consider effect on device, network, business operations before deploying |

### Cloud-based productivity tools

Covers email systems, cloud storage (with sync/folder settings), and collaboration tools (spreadsheets, videoconferencing, word processing, instant messaging) — configured alongside **identity synchronization** and **licensing assignment** for each user.

---

## 💻 Examples

```text
"A downloaded macOS installer needs to be mounted before the app can be
dragged into Applications."
→ .dmg file

"A technician needs to view real-time CPU/memory usage on a Linux server
from the terminal."
→ top

"A technician needs elevated privileges for a single command on Linux
without switching to the root account entirely."
→ sudo

"A new laptop deployment needs a specific application pushed to 200
machines without an installer for each one."
→ Image deployment

"A company migrates email and file storage to a cloud suite and needs
each employee's account tied to their existing corporate identity."
→ Identity synchronization
```

---

## 🚨 Common Mistakes

- Confusing `su` (switch user, often to root, staying in that session) with `sudo` (run a single command with elevated privileges)
- Assuming `.app` is an installer — it's the application itself; `.pkg` is the installer format
- Treating 32-bit vs. 64-bit as a minor detail — an incompatible architecture will simply refuse to run
- Overlooking that cloud productivity tools require licensing assignment per user, not just enabling a feature

---

## 📖 Further Reading

- [CompTIA A+ Core 2 (220-1202) official exam objectives](https://www.comptia.org/certifications/a)

---

## 🔗 Related Notes

- [[Operating System Types and Installation]]
- [[Choosing Between Windows, macOS, and Linux Tools]] — drill note
- [[A+ Core 2 Codex]]
