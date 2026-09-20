---
type: concept
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Intermediate
tags:
  - powershell-fundamentals
  - cim
  - wmi
aliases:
  - Get-CimInstance
  - Get-WmiObject
  - WMI
publish: true
permalink: powershell/cim-and-wmi
---

# <span class="rune">ᛟ</span> CIM and WMI

> *Almost every deep hardware or OS fact a script can't get any other way — BIOS serials, installed hotfixes, disk health — lives behind CIM. It's the layer PowerShell talks to when there's no dedicated cmdlet for the job.*

---

## 🎯 Purpose

WMI (Windows Management Instrumentation) is Windows's long-standing management infrastructure, exposing hardware, OS, and configuration data as a queryable set of classes. CIM (Common Information Model) is the modern, standards-based interface that replaced the older WMI-specific cmdlets. `Get-CimInstance` is the current, recommended way to query this same underlying data — `Get-WmiObject` is its deprecated predecessor, removed entirely in PowerShell 7+.

This matters for troubleshooting because a large share of "pertinent computer information" — BIOS version, disk SMART status, installed software, hardware inventory — has no purpose-built cmdlet and can only be retrieved through a CIM/WMI class query.

---

## 🧠 Key Ideas

- `Get-CimInstance -ClassName <WMI class>` is the modern syntax; `Get-WmiObject -Class <WMI class>` is the legacy equivalent (Windows PowerShell 5.1 only, absent from PowerShell 7+).
- WMI organizes data into **classes** under namespaces — `Win32_BIOS`, `Win32_OperatingSystem`, `Win32_LogicalDisk`, `Win32_Product` are among the most commonly queried.
- `Get-CimInstance` uses **WSMan/WinRM** as its remoting transport by default, while `Get-WmiObject` used the older DCOM/RPC transport — this is part of why CIM is more firewall-friendly for remote queries.
- WQL (WMI Query Language, a SQL-like syntax) can be used via the `-Filter` parameter for more targeted queries instead of pulling every instance and filtering client-side.
- `Get-CimClass` lists available WMI classes and their properties — the primary discovery tool when you know roughly what data you need but not the exact class name.

---

## ⚙️ How It Works

WMI classes model real system components as structured data — `Win32_BIOS` exposes BIOS version and serial number, `Win32_LogicalDisk` exposes each drive's size and free space, `Win32_Product` enumerates MSI-installed software. `Get-CimInstance` queries these classes through the CIM/WSMan infrastructure and returns them as ordinary PowerShell objects, so everything from [[Objects in PowerShell]] and [[The Pipeline]] applies immediately — sort, filter, and select against CIM results exactly like any other cmdlet's output.

CIM sessions (`New-CimSession`) let a script authenticate once and reuse an open remote connection across multiple queries, which is significantly more efficient than opening a new connection per query when gathering several pieces of information from the same remote machine.

```text
Get-CimInstance -ClassName Win32_BIOS -ComputerName srv01
        ↓
  WSMan/WinRM connection to srv01
        ↓
  CIM provider queries the BIOS WMI class locally on srv01
        ↓
  results returned as PowerShell objects to the local session
```

---

## 💻 Examples

```powershell
# Modern syntax — BIOS info
Get-CimInstance -ClassName Win32_BIOS

# Disk free space across all logical drives
Get-CimInstance -ClassName Win32_LogicalDisk -Filter "DriveType=3" |
    Select-Object DeviceID, @{N='FreeGB';E={[math]::Round($_.FreeSpace/1GB,2)}}

# Querying a remote computer
Get-CimInstance -ClassName Win32_OperatingSystem -ComputerName 'srv01'

# Reusable session for multiple queries against the same remote machine
$session = New-CimSession -ComputerName 'srv01'
Get-CimInstance -ClassName Win32_BIOS -CimSession $session
Get-CimInstance -ClassName Win32_ComputerSystem -CimSession $session
Remove-CimSession $session

# Discovering available classes matching a keyword
Get-CimClass -ClassName Win32_*Disk*
```

---

## 🚀 Real World Applications

- Pulling BIOS serial numbers and asset tags for hardware inventory during troubleshooting or audits
- Checking free disk space across every drive on a machine when a "disk full" complaint comes in
- Querying `Win32_Product` or `Win32_QuickFixEngineering` to check installed software or patch history
- Gathering OS build number, install date, and last boot time from `Win32_OperatingSystem` in a single query

---

## ⚖️ Advantages

- Exposes an enormous range of hardware and OS data that has no dedicated, purpose-built cmdlet.
- CIM's WSMan transport is more firewall-friendly for remote queries than WMI's legacy DCOM transport.
- Returns real PowerShell objects, so all standard filtering/sorting/selecting tooling works without extra parsing.
- CIM sessions amortize connection setup cost across multiple queries against the same remote machine.

---

## ⚠️ Limitations

- Class and property names are frequently unintuitive (`Win32_LogicalDisk.FreeSpace` is in bytes, not a friendly unit) and require lookup or `Get-Member`.
- `Win32_Product` in particular is notoriously slow and can trigger MSI reconfiguration as a side effect of querying it — often better avoided in favor of registry-based inventory methods for large-scale software audits.
- Remote CIM queries require WinRM to be enabled and properly configured on the target — not always the case in locked-down environments.
- Some data (especially newer hardware telemetry) has moved to PowerShell-native cmdlets or different namespaces, so not everything is best retrieved through classic `Win32_*` classes anymore.

---

## 🚨 Common Mistakes

- Using `Get-WmiObject` in scripts intended to run on PowerShell 7+ — the cmdlet doesn't exist there at all; `Get-CimInstance` is required.
- Querying `Win32_Product` for a quick software inventory without knowing about its slow performance and MSI-reconfiguration side effect on every machine it touches.
- Forgetting `FreeSpace`/`Size` properties on disk-related classes are in raw bytes, and displaying huge unformatted numbers instead of converting to GB.
- Assuming a WMI class exists identically across every Windows version — some classes are deprecated, renamed, or simply absent on older/newer builds.

---

## 📖 Further Reading

- `Get-Help Get-CimInstance -Full`
- `Get-Help about_WQL`
- Microsoft Learn: "WMI classes" reference (`Win32_*` class documentation)

---

## 💡 Wisdom from Mímir

When troubleshooting calls for a fact that "there's no cmdlet for," don't assume it's unreachable — check whether a `Win32_*` CIM class exposes it first. Nearly every deep hardware and OS detail Windows tracks internally is queryable this way.

---

## 🔗 Related Notes

- [[Computer and System Information Cmdlets]]
- [[Hardware Inventory Cmdlets]]
- [[Objects in PowerShell]]
- [[PowerShell Codex]]
