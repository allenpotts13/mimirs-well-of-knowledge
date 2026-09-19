---
type: powershell-pattern
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Beginner
tags:
  - hardware
  - storage
  - disk-space
aliases:
  - Get-Volume Free Space
  - Disk Space Report PowerShell
publish: true
permalink: powershell/finding-free-disk-space-across-all-drives
---

# <span class="rune">ᚲ</span> Finding Free Disk Space Across All Drives

> *Raw byte counts are technically correct and practically useless in a report meant for a human — the entire value of this pattern is in the conversion, not the query.*

---

## 🎯 Problem

A "disk full" complaint comes in, or a scheduled report needs to flag any machine running low on free space, across every mounted drive — not just C:.

More generally, this pattern answers:
> How much free space is available on each drive of this machine, in a readable format, and how do I flag drives below a threshold?

---

## 🤔 Mental Model

Treat `Get-Volume` as reading the fuel gauge on every tank a machine has, not just the main one — a server can have plenty of room on C: while a data drive is nearly full, and a check that only looks at the system drive misses that entirely. The goal is a complete picture across every mounted volume, converted into units a human report can actually use at a glance.

---

## 🧠 Why This Pattern Works

As covered generally in [[Storage Cmdlets]], `Get-Volume` returns `Size` and `SizeRemaining` as raw byte values for every mounted volume — calculated properties in `Select-Object` convert those into friendly GB figures and compute a percentage-free figure in the same pass, turning a technically-correct-but-unreadable number into something immediately usable in a report or an alert.

---

## 💻 PowerShell Solution

```powershell
# Free space across every drive, in a readable format
Get-Volume | Where-Object DriveLetter | Select-Object DriveLetter,
    FileSystemLabel,
    @{N='SizeGB';E={[math]::Round($_.Size/1GB,2)}},
    @{N='FreeGB';E={[math]::Round($_.SizeRemaining/1GB,2)}},
    @{N='PercentFree';E={[math]::Round(($_.SizeRemaining/$_.Size)*100,1)}}

# Flagging drives below a threshold (e.g., less than 10% free)
Get-Volume | Where-Object {
    $_.DriveLetter -and ($_.SizeRemaining / $_.Size) -lt 0.10
} | Select-Object DriveLetter, @{N='PercentFree';E={[math]::Round(($_.SizeRemaining/$_.Size)*100,1)}}
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Filter to actual mounted, lettered volumes

```powershell
Where-Object DriveLetter
```

`Get-Volume` also returns system/reserved volumes without a drive letter (recovery partitions, EFI system partitions) — filtering to only entries with a `DriveLetter` keeps the report focused on volumes a user or application would actually interact with.

### Step 2 — Convert raw bytes into readable units

```powershell
@{N='FreeGB';E={[math]::Round($_.SizeRemaining/1GB,2)}}
```

`SizeRemaining` and `Size` are raw bytes — dividing by `1GB` (a built-in PowerShell numeric literal suffix) and rounding produces a clean, human-readable figure.

### Step 3 — Compute a percentage for threshold-based alerting

```powershell
@{N='PercentFree';E={[math]::Round(($_.SizeRemaining/$_.Size)*100,1)}}
```

A raw GB figure alone doesn't distinguish "500GB free on a 512GB drive" from "500GB free on a 4TB drive" — percentage-free is the more meaningful figure for flagging genuinely concerning drives regardless of their total size.

---

## 🚀 Common Use Cases

- Responding to a "disk full" or "running out of space" complaint by checking every drive, not just C:
- Scheduled fleet-wide reporting that flags any machine with a drive below a defined free-space threshold
- Confirming enough free space exists before a large software deployment or Windows Update install
- Investigating a data drive filling up faster than expected as an early sign of a runaway log or backup process

---

## ⚖️ Alternatives

### Fleet-wide low-disk-space report

```powershell
$computers = 'ws001','ws002','srv01'
Invoke-Command -ComputerName $computers -ScriptBlock {
    Get-Volume | Where-Object { $_.DriveLetter -and ($_.SizeRemaining / $_.Size) -lt 0.10 } |
        Select-Object @{N='ComputerName';E={$env:COMPUTERNAME}}, DriveLetter,
            @{N='PercentFree';E={[math]::Round(($_.SizeRemaining/$_.Size)*100,1)}}
} | Format-Table -AutoSize
```

Scales the same threshold check across an entire fleet via [[Remoting]], surfacing only the machines actually running low.

### Legacy CIM-based approach

```powershell
Get-CimInstance -ClassName Win32_LogicalDisk -Filter "DriveType=3" |
    Select-Object DeviceID, @{N='FreeGB';E={[math]::Round($_.FreeSpace/1GB,2)}}
```

An older path to similar data through [[CIM and WMI]] — `Get-Volume` is generally the more modern, readable choice, but this remains useful on older systems or when the `Storage` module isn't available.

---

## ⚠️ Common Mistakes

- Not filtering out unlettered system/reserved volumes, cluttering a report with entries that aren't meaningful to whoever's reading it.
- Reporting raw GB free without a percentage figure, making it hard to judge severity across drives of very different total sizes.
- Checking only the C: drive out of habit, missing a data or log drive that's actually the one nearing capacity.
- Forgetting `Size`/`SizeRemaining` are raw bytes and displaying an enormous unformatted number in a report meant for a non-technical audience.

---

## 💡 Wisdom from Mímir

Always report percentage-free alongside (or instead of) raw GB free — "40GB free" sounds fine until you realize it's 40GB free on a 42GB drive, a distinction a percentage figure surfaces immediately and a raw byte count hides completely.

---

## 🔗 Related Notes

- [[Storage Cmdlets]]
- [[Checking Disk Health and SMART Status]]
- [[Identifying What's Filling Up a Disk]]
- [[CIM and WMI]]
