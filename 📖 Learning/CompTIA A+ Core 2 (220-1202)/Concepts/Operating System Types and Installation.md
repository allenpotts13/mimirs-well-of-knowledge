---
type: concept
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA A+
difficulty: Beginner
tags:
  - a-plus-1202
  - operating-systems
aliases:
  - A+ OS Types and Installation
publish: true
permalink: comptia-a-core-2-220-1202/operating-system-types-and-installation
---

# <span class="rune">ᛟ</span> Operating System Types and Installation

> *Before you can troubleshoot Windows, you have to know how it got installed and why that edition matters.*

---

## 🎯 Purpose

Covers 1.1-1.3 of the 28%-weighted Operating Systems domain: OS types (workstation/mobile, filesystems), installation/upgrade methods, and Windows edition differences.

---

## 🧠 Key Ideas

- Workstation OSs (Windows, Linux, macOS, Chrome OS) and mobile OSs (iOS/iPadOS, Android) are a different axis from **filesystem type** (NTFS, ReFS, FAT32, ext4, APFS, exFAT)
- Boot methods (USB, network, internal drive, multiboot) determine *where* the installer runs from; installation type (clean vs. upgrade) determines *what happens to existing data*
- Partitioning uses **GPT** (modern, supports larger drives, UEFI) or **MBR** (legacy, BIOS, 2TB limit)
- Windows editions differ mainly in **feature ceiling** (RAM support, BitLocker, RDP host capability, domain-join) — Home is the most limited, Pro/Enterprise unlock business features
- Upgrade considerations always include hardware compatibility, driver support, and backing up files/preferences first

---

## ⚙️ How It Works

### Filesystem types

| Filesystem | Platform | Note |
|---|---|---|
| NTFS | Windows | Standard modern Windows filesystem, supports permissions/encryption |
| ReFS | Windows Server | Resilient, self-healing, for large storage |
| FAT32 | Cross-platform | 4GB file size limit, no permissions |
| exFAT | Cross-platform | FAT32 successor, no 4GB limit, no permissions |
| ext4 | Linux | Standard Linux filesystem |
| APFS | macOS | Modern Apple filesystem |

### Installation types

| Type | What happens |
|---|---|
| Clean install | Wipes and installs fresh — no data preserved |
| Upgrade | Preserves apps/settings/data, migrates to new OS version |
| Repair installation | Fixes a broken OS without wiping data |
| Image deployment | Deploys a pre-built OS image to many machines |
| Remote network install | Installs over the network (PXE boot, zero-touch deployment) |

### Partitioning: GPT vs. MBR

| | GPT | MBR |
|---|---|---|
| Firmware | UEFI | Legacy BIOS |
| Max drive size | >2TB | 2TB limit |
| Max partitions | 128 | 4 primary |

### Windows editions (feature ceiling)

| Edition | Notable capability |
|---|---|
| Home | No domain join, no BitLocker, no RDP host, RAM cap lower |
| Pro | Domain join, BitLocker, RDP host (can be connected *to*) |
| Pro for Workstations | Higher RAM/CPU ceiling for high-end hardware |
| Enterprise | Full management/deployment feature set for organizations |

### Upgrade considerations

Before upgrading: back up files and preferences, confirm hardware compatibility (especially **TPM** and **UEFI** requirements for Windows 11), verify application/driver backward compatibility, and check the product's feature life cycle/EOL status.

---

## 💻 Examples

```text
"A drive needs to support a 5TB single partition."
→ GPT (MBR caps out at 2TB)

"A user's files, apps, and settings all need to survive moving from an
older Windows version to a newer one."
→ Upgrade install, not clean install

"A workstation needs to join a corporate domain and use BitLocker."
→ Windows Pro or higher — Home doesn't support either

"A USB drive is being formatted to share files between a Windows PC and a
Mac, with files potentially larger than 4GB."
→ exFAT
```

---

## 🚨 Common Mistakes

- Assuming any Windows edition supports domain join and BitLocker — Home does not
- Choosing FAT32 for large file sharing and hitting the 4GB file size ceiling unexpectedly
- Treating "upgrade" and "in-place repair" as the same thing — repair fixes a broken install without changing the OS version
- Forgetting Windows 11 has hard TPM/UEFI requirements that block installation on older hardware

---

## 📖 Further Reading

- [CompTIA A+ Core 2 (220-1202) official exam objectives](https://www.comptia.org/certifications/a)

---

## 🔗 Related Notes

- [[Windows Administration Tools and Command Line]]
- [[A+ Core 2 Codex]]
