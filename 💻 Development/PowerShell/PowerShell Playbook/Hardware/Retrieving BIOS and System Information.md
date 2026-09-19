---
type: powershell-pattern
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Beginner
tags:
  - hardware
  - bios
  - asset-management
aliases:
  - Get-CimInstance Win32_BIOS
  - System Serial Number PowerShell
publish: true
permalink: powershell/retrieving-bios-and-system-information
---

# <span class="rune">ᚲ</span> Retrieving BIOS and System Information

> *An asset tag on a sticker can be scratched off or wrong. The serial number embedded in the BIOS itself is the one fact about a machine that travels with the hardware no matter what's been relabeled.*

---

## 🎯 Problem

An asset audit, warranty check, or hardware ticket needs the machine's manufacturer, model, and serial number — reliably and remotely, without physically inspecting a sticker on the case.

More generally, this pattern answers:
> What are this machine's core hardware identity facts — manufacturer, model, serial number, and BIOS version?

---

## 🤔 Mental Model

Treat the BIOS/system CIM classes as the machine's own birth certificate — facts written into firmware at the factory, independent of whatever OS is installed, whatever asset tag sticker has been applied, or how many times the machine has been reimaged. Querying `Win32_BIOS` and `Win32_ComputerSystem` reads that certificate directly, rather than relying on any potentially outdated or mislabeled external record.

---

## 🧠 Why This Pattern Works

`Win32_BIOS` exposes the serial number and BIOS version burned into firmware at manufacturing time, while `Win32_ComputerSystem` exposes the manufacturer and model — both queryable through [[CIM and WMI|CIM]] locally or remotely without any specialized hardware tooling. Because this data lives in firmware rather than the OS installation, it survives a full OS reimage completely intact, making it a more durable identifier than anything stored at the software layer.

---

## 💻 PowerShell Solution

```powershell
# Local machine — core identity facts in one combined report
$bios = Get-CimInstance -ClassName Win32_BIOS
$system = Get-CimInstance -ClassName Win32_ComputerSystem

[pscustomobject]@{
    Manufacturer = $system.Manufacturer
    Model        = $system.Model
    SerialNumber = $bios.SerialNumber
    BIOSVersion  = $bios.SMBIOSBIOSVersion
    TotalRAMGB   = [math]::Round($system.TotalPhysicalMemory / 1GB, 2)
}

# Remote machine, same report
Invoke-Command -ComputerName 'ws042' -ScriptBlock {
    $bios = Get-CimInstance -ClassName Win32_BIOS
    $system = Get-CimInstance -ClassName Win32_ComputerSystem
    [pscustomobject]@{
        Manufacturer = $system.Manufacturer
        Model        = $system.Model
        SerialNumber = $bios.SerialNumber
        BIOSVersion  = $bios.SMBIOSBIOSVersion
    }
}
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Query the BIOS class for serial and firmware version

```powershell
Get-CimInstance -ClassName Win32_BIOS
```

`SerialNumber` and `SMBIOSBIOSVersion` are the two most commonly needed fields — the serial number in particular is what most warranty lookup tools and asset systems expect.

### Step 2 — Query the computer system class for manufacturer/model

```powershell
Get-CimInstance -ClassName Win32_ComputerSystem
```

`Manufacturer` and `Model` together identify exactly which hardware line and configuration the machine is — needed for driver, warranty, and compatibility lookups.

### Step 3 — Combine into one clean report object

```powershell
[pscustomobject]@{ Manufacturer = ...; Model = ...; SerialNumber = ...; BIOSVersion = ... }
```

Combining both CIM classes' relevant fields into a single object makes the result immediately usable in a report or export, rather than requiring two separate queries to be manually cross-referenced.

---

## 🚀 Common Use Cases

- Pulling a serial number for a manufacturer warranty status lookup
- Building or reconciling an asset inventory against physical hardware records
- Confirming exact hardware model before checking driver or firmware compatibility
- Fleet-wide hardware inventory reporting across many machines via [[Remoting]]

---

## ⚖️ Alternatives

### Fleet-wide serial number collection for an asset audit

```powershell
$computers = 'ws001','ws002','ws042'
Invoke-Command -ComputerName $computers -ScriptBlock {
    [pscustomobject]@{
        ComputerName = $env:COMPUTERNAME
        SerialNumber = (Get-CimInstance Win32_BIOS).SerialNumber
        Model        = (Get-CimInstance Win32_ComputerSystem).Model
    }
} | Export-Csv -Path .\hardware-inventory.csv -NoTypeInformation
```

Scales the exact same query across an entire fleet in one pass for a consolidated asset report.

### Checking BIOS version specifically ahead of a firmware update

```powershell
(Get-CimInstance -ClassName Win32_BIOS).SMBIOSBIOSVersion
```

A focused check when the goal is confirming whether a firmware update is actually needed, rather than a full identity report.

---

## ⚠️ Common Mistakes

- Relying on a physical asset tag sticker as the authoritative serial number when it can be damaged, mislabeled, or belong to a machine that had its case swapped during a repair — the BIOS-reported serial is the more reliable source of truth.
- Not accounting for virtual machines, where `Win32_BIOS.SerialNumber` often returns a generic hypervisor-assigned value rather than anything meaningful for physical asset tracking.
- Assuming `Win32_ComputerSystem.Model` is always a clean, human-readable string — some OEMs populate this field with internal part numbers that need cross-referencing against the manufacturer's own model lookup.
- Querying this data across a large fleet sequentially instead of using `Invoke-Command`'s native support for multiple `-ComputerName` values, making a large audit unnecessarily slow.

---

## 💡 Wisdom from Mímir

When an asset record and a physical sticker disagree about a machine's serial number, trust the BIOS-reported value from `Win32_BIOS` over either of them — it's the one fact about the hardware that can't be relabeled, misapplied, or lost to a worn-off sticker.

---

## 🔗 Related Notes

- [[Hardware Inventory Cmdlets]]
- [[CIM and WMI]]
- [[Computer and System Information Cmdlets]]
- [[Listing Connected USB and Plug-and-Play Devices]]
