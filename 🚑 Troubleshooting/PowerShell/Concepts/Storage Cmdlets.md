---
type: concept
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Intermediate
tags:
  - powershell-domain-reference
  - storage
  - disks
aliases:
  - Get-Disk
  - Get-Volume
  - Get-PhysicalDisk
publish: true
permalink: powershell/storage-cmdlets
---

# <span class="rune">ᛟ</span> Storage Cmdlets

> *Windows exposes storage as three distinct layers — the physical disk, the partition, and the volume — and the `Storage` module gives each layer its own dedicated cmdlets instead of collapsing them into one.*

---

## 🎯 Purpose

The `Storage` module provides modern, object-based cmdlets for inspecting and managing disks, partitions, and volumes, replacing the disk-related uses of `diskpart` and much of the legacy `Win32_LogicalDisk`/`Win32_DiskDrive` CIM querying with a cleaner, purpose-built API. It also exposes SMART/reliability health data directly, which is central to proactive and reactive disk-failure troubleshooting.

---

## 🧠 Key Ideas

- **Physical disks** (`Get-PhysicalDisk`) are the actual hardware devices — SSD/HDD, media type, and `HealthStatus`.
- **Disks** (`Get-Disk`) sit one layer up — partition style (GPT/MBR), online/offline state, and total size.
- **Partitions** (`Get-Partition`) are the divisions within a disk; **volumes** (`Get-Volume`) are the filesystem-formatted, drive-lettered layer users and applications actually interact with.
- `Get-PhysicalDisk | Get-StorageReliabilityCounter` exposes SMART-derived reliability data (read/write error counts, temperature, wear) directly as PowerShell objects — no third-party tool required.
- `Get-Volume` reports `SizeRemaining`/`Size` directly as numeric byte values, making free-space calculations and reporting straightforward without CIM's less intuitive `Win32_LogicalDisk` property names.

---

## ⚙️ How It Works

The `Storage` module models the same physical reality as `diskpart`, but as a proper object hierarchy: a `Disk` object contains `Partition` objects, which in turn are associated with `Volume` objects representing what's actually mounted and drive-lettered. This layered model is why "disk space" troubleshooting sometimes needs `Get-Volume` (what's the C: drive's free space) and other times needs `Get-PhysicalDisk`/`Get-StorageReliabilityCounter` (is the underlying hardware itself failing) — they answer genuinely different questions about the same storage stack.

```text
Get-PhysicalDisk   → the actual hardware (SSD/HDD), HealthStatus
        ↓
Get-Disk           → the disk as Windows sees it (GPT/MBR, online/offline)
        ↓
Get-Partition      → divisions within that disk
        ↓
Get-Volume         → the filesystem/drive letter layer (C:, D:, etc.) — what "disk space" usually means
```

---

## 💻 Examples

```powershell
# Physical disk health at a glance
Get-PhysicalDisk | Select-Object DeviceId, MediaType, HealthStatus, OperationalStatus

# SMART/reliability counters — proactive failure detection
Get-PhysicalDisk | Get-StorageReliabilityCounter |
    Select-Object DeviceId, Temperature, ReadErrorsTotal, Wear

# Disk-level info (partition style, online state)
Get-Disk

# Volume-level free space, in friendly units
Get-Volume | Select-Object DriveLetter, FileSystemLabel,
    @{N='SizeGB';E={[math]::Round($_.Size/1GB,2)}},
    @{N='FreeGB';E={[math]::Round($_.SizeRemaining/1GB,2)}}

# Partition layout for a specific disk
Get-Disk -Number 0 | Get-Partition
```

---

## 🚀 Real World Applications

- Checking disk health status proactively before a drive fails outright during a support call about a "disk full" complaint
- Reporting free space across all mounted volumes in a clean, ready-to-export format
- Investigating whether a slow machine's real bottleneck is disk wear/errors rather than CPU or memory
- Confirming whether a newly attached disk is online, offline, or uninitialized before troubleshooting why it isn't showing a drive letter

---

## ⚖️ Advantages

- Native SMART/reliability data access without needing a third-party disk-health utility.
- Clean layered model (physical disk → disk → partition → volume) makes it clear which layer a given problem actually lives in.
- Byte-based numeric properties (`Size`, `SizeRemaining`) are trivially converted to friendly units, unlike some legacy WMI property naming.
- Works consistently across local and remote (`-CimSession`) queries for fleet-wide storage health checks.

---

## ⚠️ Limitations

- `Get-StorageReliabilityCounter` support and populated fields vary by disk hardware/driver — not every disk reports every counter reliably.
- The Storage module's cmdlets are Windows-specific (built on Storage Management API), not available identically on non-Windows PowerShell.
- Requires an elevated session for most write operations (initializing disks, creating partitions) though read operations generally don't.
- `HealthStatus: Healthy` on `Get-PhysicalDisk` doesn't guarantee zero problems — it reflects the storage subsystem's own assessment, which can lag behind an actual developing hardware failure.

---

## 🚨 Common Mistakes

- Checking `Get-Volume` free space when the actual question was about physical disk health (or vice versa) — these are genuinely different layers with different failure modes.
- Assuming a disk showing as `Offline` in `Get-Disk` is a hardware failure, when it's very often just a Windows disk management state that needs `Set-Disk -IsOffline $false`.
- Not checking `Get-StorageReliabilityCounter` proactively, missing early warning signs (rising error counts, high temperature) before a drive fails outright.
- Forgetting `SizeRemaining`/`Size` are raw bytes and displaying huge unformatted numbers in a report meant for a non-technical reader.

---

## 📖 Further Reading

- `Get-Help Get-PhysicalDisk -Full`
- `Get-Help Get-StorageReliabilityCounter -Full`
- Microsoft Learn: "Storage cmdlets in Windows PowerShell"

---

## 💡 Wisdom from Mímir

When a "disk full" ticket comes in, check `Get-Volume` for free space AND `Get-PhysicalDisk | Get-StorageReliabilityCounter` for health in the same pass — a nearly-full disk and a failing disk produce similar user complaints but require completely different remediation.

---

## 🔗 Related Notes

- [[Checking Disk Health and SMART Status]]
- [[Finding Free Disk Space Across All Drives]]
- [[CIM and WMI]]
- [[PowerShell Codex]]
