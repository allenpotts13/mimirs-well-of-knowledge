---
type: powershell-pattern
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Beginner
tags:
  - hardware
  - usb
  - device-manager
aliases:
  - Get-PnpDevice USB
  - Device Manager PowerShell
publish: true
permalink: powershell/listing-connected-usb-and-plug-and-play-devices
---

# <span class="rune">ᚲ</span> Listing Connected USB and Plug-and-Play Devices

> *A yellow warning triangle in Device Manager is a status Windows already computed — `Get-PnpDevice -Status Error` just reads that same verdict without needing a mouse.*

---

## 🎯 Problem

A peripheral (a scanner, a specialized USB device, a docking station) isn't working, and the cause needs to be narrowed down to a genuine hardware/driver problem versus something else — without physically opening Device Manager on the machine.

More generally, this pattern answers:
> What Plug-and-Play devices does this machine currently see, and which ones (if any) are Windows itself flagging as broken?

---

## 🤔 Mental Model

Think of `Get-PnpDevice` as Device Manager's tree, exposed as data instead of a window — the same devices, the same status icons (represented as a `Status` property instead of a colored icon), just queryable and scriptable instead of requiring someone to physically click through it on that one machine.

---

## 🧠 Why This Pattern Works

As introduced generally in [[Hardware Inventory Cmdlets]], `Get-PnpDevice` reflects the live Plug-and-Play device tree Windows itself maintains — `Status: Error` specifically means Windows' own driver stack has already flagged that device as malfunctioning, which is the scriptable equivalent of the yellow warning triangle a technician would otherwise have to notice visually in Device Manager.

---

## 💻 PowerShell Solution

```powershell
# Any device Windows has flagged as having a problem — the fastest triage step
Get-PnpDevice -Status Error | Select-Object FriendlyName, InstanceId, Class

# All currently connected USB devices specifically
Get-PnpDevice -Class USB -Status OK | Select-Object FriendlyName, InstanceId

# Checking a specific device's extended properties (e.g., driver version)
$device = Get-PnpDevice -Class USB | Where-Object FriendlyName -like '*Scanner*'
Get-PnpDeviceProperty -InstanceId $device.InstanceId -KeyName 'DEVPKEY_Device_DriverVersion'

# Remote check via Invoke-Command
Invoke-Command -ComputerName 'ws042' -ScriptBlock {
    Get-PnpDevice -Status Error | Select-Object FriendlyName, Class
}
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Check for flagged problem devices first

```powershell
Get-PnpDevice -Status Error
```

The fastest possible triage step on any vague "something's not working" hardware ticket — immediately surfaces anything Windows has already identified as broken, without needing to know which device is suspected in advance.

### Step 2 — Narrow to a specific device class or name if the problem device is already suspected

```powershell
Get-PnpDevice -Class USB | Where-Object FriendlyName -like '*Scanner*'
```

Useful once the general area (USB peripherals specifically, in this case) is known, to zero in on the exact device.

### Step 3 — Pull extended properties for deeper diagnosis

```powershell
Get-PnpDeviceProperty -InstanceId $device.InstanceId -KeyName 'DEVPKEY_Device_DriverVersion'
```

`Get-PnpDevice`'s default view is a summary — extended properties like exact driver version, hardware ID, or last driver update date require this follow-up call with a specific property key.

---

## 🚀 Common Use Cases

- First-triage step on any vague hardware-suspected ticket, before diving into a specific suspected device
- Confirming a peripheral is actually recognized by Windows at all versus a deeper connectivity/power problem
- Investigating driver issues after a Windows Update by checking for newly-flagged `Status: Error` devices
- Remote diagnosis of a peripheral issue without needing physical access to check Device Manager directly

---

## ⚖️ Alternatives

### Listing every device regardless of status, for a full inventory

```powershell
Get-PnpDevice | Group-Object Class | Sort-Object Count -Descending | Select-Object Name, Count
```

Useful for a broader hardware inventory pass rather than specifically hunting for problems.

### Combining with BIOS/system info for a full hardware snapshot

```powershell
[pscustomobject]@{
    SerialNumber  = (Get-CimInstance Win32_BIOS).SerialNumber
    ProblemDevices = (Get-PnpDevice -Status Error).FriendlyName -join '; '
}
```

Pairs with [[Retrieving BIOS and System Information]] to build a single combined hardware health snapshot for a machine.

---

## ⚠️ Common Mistakes

- Assuming `Status: OK` guarantees full device functionality — it only reflects whether the OS driver stack reports a clean state, not whether the physical device itself is electrically healthy or actually working as expected.
- Not running the check elevated when investigating devices requiring administrative visibility — some device properties are only fully visible in an elevated session.
- Confusing `Class` (a broad category like USB, Net, DiskDrive) with the specific device name — filtering only by class on a machine with many similar devices can still return more results than expected.
- Overlooking that a device physically unplugged still often appears in `Get-PnpDevice` output (in a disconnected state) rather than disappearing entirely — checking `Status`/`Present` fields matters more than just checking whether the device shows up at all.

---

## 💡 Wisdom from Mímir

Make `Get-PnpDevice -Status Error` the reflexive first command on any vague hardware ticket — it takes one line, requires no prior knowledge of which device is suspected, and very often points directly at the actual problem before any other investigation begins.

---

## 🔗 Related Notes

- [[Hardware Inventory Cmdlets]]
- [[Retrieving BIOS and System Information]]
- [[CIM and WMI]]
- [[Finding Which Process Is Locking a File]]
