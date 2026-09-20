---
type: concept
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Intermediate
tags:
  - powershell-domain-reference
  - hardware-inventory
aliases:
  - Get-PnpDevice
  - Hardware Inventory PowerShell
publish: true
permalink: powershell/hardware-inventory-cmdlets
---

# <span class="rune">ᛟ</span> Hardware Inventory Cmdlets

> *Everything Device Manager shows you is backed by a queryable object — `Get-PnpDevice` and a handful of `Win32_*` classes turn a GUI you'd have to click through into a report you can generate in one line.*

---

## 🎯 Purpose

Detailed hardware inventory — BIOS/firmware details, installed RAM, CPU model, and connected plug-and-play devices — combines the modern `PnpDevice` cmdlets with targeted [[CIM and WMI|CIM class]] queries. This is the toolkit behind hardware compatibility checks, asset tracking, and diagnosing "is this a hardware problem" tickets without opening the case or navigating Device Manager by hand.

---

## 🧠 Key Ideas

- `Get-PnpDevice` lists every Plug-and-Play device Windows recognizes — USB peripherals, network adapters, storage controllers — each with a `Status` (OK, Error, Unknown) and `Class`.
- `Win32_BIOS` exposes BIOS version, serial number, and manufacturer; `Win32_ComputerSystem` exposes system model, manufacturer, and total physical memory; `Win32_Processor` exposes CPU model, core count, and clock speed.
- `Win32_PhysicalMemory` returns one object **per physical RAM stick** — capacity, speed, and slot — distinct from `Win32_ComputerSystem.TotalPhysicalMemory`, which is the single aggregate total.
- `Get-PnpDevice -Status Error` immediately surfaces devices Windows has flagged as malfunctioning — often the fastest way to spot a failing or driver-less peripheral.
- `Get-PnpDeviceProperty` retrieves extended properties (like a device's exact driver version or hardware ID) beyond what `Get-PnpDevice`'s default view shows.

---

## ⚙️ How It Works

`Get-PnpDevice` reflects the same Plug-and-Play device tree Device Manager visualizes graphically — pulling from it directly gives every device's real-time status without opening a single GUI window. Combined with the `Win32_*` CIM classes covering BIOS, processor, and memory, this gives a complete hardware snapshot built entirely from queries rather than manual inspection — which is exactly what makes it scriptable across an entire fleet of machines at once, not just one at a time.

```text
Get-PnpDevice -Status Error           → devices Windows itself has flagged as broken
Win32_PhysicalMemory (per stick)      → individual RAM module detail
Win32_ComputerSystem.TotalPhysicalMemory → the single aggregate total, for a quick summary
```

---

## 💻 Examples

```powershell
# Devices Windows has flagged as having a problem
Get-PnpDevice -Status Error

# All USB devices currently connected
Get-PnpDevice -Class USB -Status OK

# BIOS details
Get-CimInstance -ClassName Win32_BIOS |
    Select-Object Manufacturer, SMBIOSBIOSVersion, SerialNumber

# CPU details
Get-CimInstance -ClassName Win32_Processor |
    Select-Object Name, NumberOfCores, MaxClockSpeed

# Per-stick RAM detail (capacity, speed, slot)
Get-CimInstance -ClassName Win32_PhysicalMemory |
    Select-Object DeviceLocator, @{N='CapacityGB';E={$_.Capacity/1GB}}, Speed

# Aggregate total RAM in one figure
(Get-CimInstance -ClassName Win32_ComputerSystem).TotalPhysicalMemory / 1GB

# Extended device property lookup
Get-PnpDevice -InstanceId (Get-PnpDevice -Class USB)[0].InstanceId |
    Get-PnpDeviceProperty -KeyName 'DEVPKEY_Device_DriverVersion'
```

---

## 🚀 Real World Applications

- Running `Get-PnpDevice -Status Error` as a first triage step on a "something's wrong with this computer" ticket with no more specific symptom
- Pulling per-slot RAM detail to confirm actual installed configuration before a memory upgrade
- Recording BIOS serial numbers as part of asset tag verification or warranty lookups
- Confirming CPU model/core count against a software vendor's minimum system requirements

---

## ⚖️ Advantages

- `Get-PnpDevice -Status Error` surfaces hardware problems immediately, without manually scanning Device Manager for yellow warning icons.
- Fully scriptable across remote machines via CIM sessions, unlike Device Manager's GUI which is inherently single-machine and manual.
- Per-stick RAM detail from `Win32_PhysicalMemory` gives information the aggregate total alone can't (e.g., confirming whether slots are fully populated or mismatched speeds).
- BIOS/serial number queries provide reliable, script-friendly asset data without needing manufacturer-specific tools.

---

## ⚠️ Limitations

- `Get-PnpDevice`'s `Status` field reflects what the OS driver stack reports — a device can be electrically failing in a way Windows hasn't detected yet, showing `Status: OK` despite an underlying hardware problem.
- Not every property is populated on every manufacturer's hardware — some OEMs leave BIOS/asset fields blank or nonstandard.
- `Win32_PhysicalMemory` speed/capacity data depends on BIOS/SMBIOS reporting accuracy, which is occasionally inconsistent across vendors.
- Extended property lookups via `Get-PnpDeviceProperty` require knowing the specific property key name in advance, which isn't always intuitive to discover.

---

## 🚨 Common Mistakes

- Reading `Win32_ComputerSystem.TotalPhysicalMemory` and expecting it in a friendly unit — it's raw bytes and needs conversion for a readable report.
- Assuming `Get-PnpDevice -Status OK` guarantees a device is fully functional, rather than just "no driver-level error currently reported."
- Confusing per-stick `Win32_PhysicalMemory` results with the aggregate `Win32_ComputerSystem` total, and reporting the wrong number in a hardware summary.
- Not checking `Get-PnpDevice -Status Error` early in a hardware-suspected ticket, spending time on software troubleshooting when a flagged device would have pointed directly at the cause.

---

## 📖 Further Reading

- `Get-Help Get-PnpDevice -Full`
- Microsoft Learn: `Win32_BIOS`, `Win32_Processor`, `Win32_PhysicalMemory` class references
- Microsoft Learn: "PnP device properties reference"

---

## 💡 Wisdom from Mímir

Make `Get-PnpDevice -Status Error` a reflex first step on any vague hardware-suspected ticket — it takes one line and often points directly at the failing component before any deeper investigation is needed.

---

## 🔗 Related Notes

- [[CIM and WMI]]
- [[Retrieving BIOS and System Information]]
- [[Listing Connected USB and Plug-and-Play Devices]]
- [[PowerShell Codex]]
