---
type: powershell-pattern
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Intermediate
tags:
  - hardware
  - storage
  - smart
  - proactive-maintenance
aliases:
  - Get-StorageReliabilityCounter
  - SMART Status PowerShell
publish: true
permalink: powershell/checking-disk-health-and-smart-status
---

# <span class="rune">ᚲ</span> Checking Disk Health and SMART Status

> *A drive rarely fails without warning — it fails after weeks of rising error counts nobody happened to be watching. Reliability counters exist specifically so that warning doesn't have to be missed.*

---

## 🎯 Problem

A machine has been behaving sluggishly, or a drive is nearing end-of-life, and it needs proactive health checking before it fails outright and takes data with it.

More generally, this pattern answers:
> Is a physical disk showing early signs of failure, and how do I check its SMART-derived reliability data from PowerShell?

---

## 🤔 Mental Model

Think of `HealthStatus` as a drive's basic pulse check — alive or not — and `Get-StorageReliabilityCounter` as its full blood panel: read/write error counts, temperature, and wear level, the numbers that reveal a developing problem *before* the drive fails outright and the pulse check finally catches it. Checking only `HealthStatus` catches failures that have already happened; checking reliability counters is what actually gives advance warning.

---

## 🧠 Why This Pattern Works

As introduced generally in [[Storage Cmdlets]], `Get-PhysicalDisk` exposes each physical drive with a basic `HealthStatus` (Healthy/Warning/Unhealthy), while `Get-StorageReliabilityCounter` surfaces the underlying SMART-derived metrics the storage subsystem is actually tracking — rising `ReadErrorsTotal`, unusual `Temperature`, or high `Wear` (on SSDs specifically) are all leading indicators that a drive is degrading, often well before `HealthStatus` itself flips away from "Healthy."

---

## 💻 PowerShell Solution

```powershell
# Quick pulse check — basic health status per physical disk
Get-PhysicalDisk | Select-Object DeviceId, MediaType, HealthStatus, OperationalStatus

# Full reliability data — the actual early-warning signal
Get-PhysicalDisk | Get-StorageReliabilityCounter |
    Select-Object DeviceId, Temperature, ReadErrorsTotal, WriteErrorsTotal, Wear

# Flagging any disk with concerning values
Get-PhysicalDisk | Get-StorageReliabilityCounter | Where-Object {
    $_.ReadErrorsTotal -gt 0 -or $_.Temperature -gt 55 -or $_.Wear -gt 80
} | Select-Object DeviceId, Temperature, ReadErrorsTotal, Wear
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Check basic health status first

```powershell
Get-PhysicalDisk | Select-Object DeviceId, MediaType, HealthStatus, OperationalStatus
```

A fast overall check — but `HealthStatus: Healthy` alone doesn't rule out a developing problem, only an already-diagnosed one.

### Step 2 — Pull detailed reliability counters for real early-warning data

```powershell
Get-PhysicalDisk | Get-StorageReliabilityCounter
```

Piping physical disk objects directly into `Get-StorageReliabilityCounter` returns SMART-derived metrics for each — the data that actually predicts failure rather than just confirming it after the fact.

### Step 3 — Define and check meaningful thresholds

```powershell
Where-Object { $_.ReadErrorsTotal -gt 0 -or $_.Temperature -gt 55 -or $_.Wear -gt 80 }
```

Any nonzero error count is worth investigating; elevated temperature or SSD wear percentage nearing 100 are both concrete, actionable early-warning signals worth flagging before they become an outright failure.

---

## 🚀 Common Use Cases

- Proactive fleet-wide disk health checks as part of scheduled preventive maintenance
- Investigating whether a slow machine's real bottleneck is a degrading disk rather than CPU/memory
- Confirming disk health before or after a hardware refresh decision, prioritizing which machines actually need replacement
- Early warning for SSD wear-level on machines nearing their expected drive lifespan

---

## ⚖️ Alternatives

### Fleet-wide health sweep

```powershell
$computers = 'ws001','ws002','ws042'
Invoke-Command -ComputerName $computers -ScriptBlock {
    Get-PhysicalDisk | Get-StorageReliabilityCounter |
        Select-Object @{N='ComputerName';E={$env:COMPUTERNAME}}, DeviceId, Temperature, ReadErrorsTotal, Wear
} | Where-Object { $_.ReadErrorsTotal -gt 0 -or $_.Wear -gt 80 }
```

Scales the same reliability check across an entire fleet, surfacing only the machines with concerning values.

### Third-party SMART tools (for hardware where native counters are sparse)

Some drives/controllers report reliability data inconsistently through the native Storage module — a dedicated third-party SMART utility can sometimes surface more complete vendor-specific detail when native counters come back mostly empty.

---

## ⚠️ Common Mistakes

- Checking only `HealthStatus` and assuming "Healthy" rules out an actively degrading drive — reliability counters often show a problem developing well before `HealthStatus` itself changes.
- Not accounting for inconsistent reliability counter support across different drive hardware/controllers — some values may return `$null` or zero simply because that specific hardware doesn't report them, not because there's no issue.
- Treating a single elevated temperature reading as conclusive without checking whether it's a one-time spike (e.g., under heavy load) versus a sustained pattern over multiple checks.
- Ignoring SSD-specific `Wear` percentage on machines approaching end-of-life, missing an opportunity for planned replacement before an unplanned failure.

---

## 💡 Wisdom from Mímir

Make disk reliability counters part of routine, scheduled checks — not just something looked at after a complaint comes in. By the time a user notices a disk problem, the reliability counters have usually been quietly climbing for weeks.

---

## 🔗 Related Notes

- [[Storage Cmdlets]]
- [[Finding Free Disk Space Across All Drives]]
- [[Hardware Inventory Cmdlets]]
- [[Identifying What's Filling Up a Disk]]
