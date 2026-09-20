---
type: powershell-pattern
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Intermediate
tags:
  - applications
  - software-inventory
  - registry
aliases:
  - List Installed Programs PowerShell
  - Get-InstalledSoftware
publish: true
permalink: powershell/finding-installed-software-on-a-machine
---

# <span class="rune">ᚲ</span> Finding Installed Software on a Machine

> *Programs and Features is a GUI reading the exact same registry keys this script reads — the only difference is one of them can be piped, filtered, and run against fifty machines at once.*

---

## 🎯 Problem

Get a complete, exportable list of every application installed on a machine — for an audit, a licensing check, or to confirm a specific piece of software is (or isn't) present.

More generally, this pattern answers:
> What software is actually installed on this machine, in a form I can filter, compare, and export?

---

## 🤔 Mental Model

Treat this exactly like checking a filing cabinet that three different filing clerks (32-bit installers, 64-bit installers, and per-user installers) each maintain their own drawer in — checking only one drawer gives an incomplete answer every time. The registry-based approach, as detailed generally in [[Software Inventory Cmdlets]], means checking all the relevant drawers in one pass rather than assuming any single one is complete on its own.

---

## 🧠 Why This Pattern Works

Programs and Features itself is just a friendly UI reading the same registry uninstall keys this script reads directly — there's no more authoritative source available through the GUI that isn't equally available through PowerShell. Building a small reusable function around this logic turns a one-off registry query into a repeatable tool usable locally or against many remote machines via [[Remoting]].

---

## 💻 PowerShell Solution

```powershell
function Get-InstalledSoftware {
    param([string]$ComputerName = $env:COMPUTERNAME)

    $paths = @(
        'SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*',
        'SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*'
    )

    $scriptBlock = {
        param($paths)
        $paths | ForEach-Object {
            Get-ItemProperty -Path "HKLM:\$_" -ErrorAction SilentlyContinue
        } | Where-Object DisplayName |
            Select-Object DisplayName, DisplayVersion, Publisher, InstallDate
    }

    if ($ComputerName -eq $env:COMPUTERNAME) {
        & $scriptBlock $paths
    } else {
        Invoke-Command -ComputerName $ComputerName -ScriptBlock $scriptBlock -ArgumentList (,$paths)
    }
}

# Local machine
Get-InstalledSoftware | Sort-Object DisplayName

# Remote machine
Get-InstalledSoftware -ComputerName 'ws042' | Sort-Object DisplayName

# Export for an audit
Get-InstalledSoftware | Export-Csv -Path .\installed-software.csv -NoTypeInformation
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Check both 64-bit and 32-bit-on-64-bit registry paths

```powershell
$paths = @('SOFTWARE\Microsoft\...\Uninstall\*', 'SOFTWARE\WOW6432Node\Microsoft\...\Uninstall\*')
```

Missing the WOW6432Node path silently omits every 32-bit application on a 64-bit OS — a very common source of an incomplete inventory.

### Step 2 — Filter out entries with no DisplayName

```powershell
Where-Object DisplayName
```

Many uninstall keys exist for system components or updates without a meaningful `DisplayName` — filtering these out leaves only genuine, user-recognizable applications.

### Step 3 — Support both local and remote execution transparently

```powershell
if ($ComputerName -eq $env:COMPUTERNAME) { & $scriptBlock $paths } else { Invoke-Command ... }
```

Wrapping the core logic as a script block usable either directly or via [[Remoting]] makes the function equally useful for a single machine or scripted across a fleet.

---

## 🚀 Common Use Cases

- Software licensing audits confirming what's actually installed versus what's licensed
- Confirming a specific application (and version) is present before troubleshooting a compatibility issue
- Building a baseline software inventory for a new deployment image or golden configuration
- Comparing installed software across multiple machines to spot configuration drift

---

## ⚖️ Alternatives

### Fleet-wide inventory across many machines at once

```powershell
$computers = 'ws001','ws002','ws042'
$computers | ForEach-Object {
    Get-InstalledSoftware -ComputerName $_ | Select-Object @{N='ComputerName';E={$_}}, DisplayName, DisplayVersion
}
```

Scales the same function across an entire list of machines for a consolidated audit report.

### Quick check via Get-Package (less complete, but no custom function needed)

```powershell
Get-Package | Where-Object Name -like '*Chrome*'
```

Faster for a one-off check when completeness isn't critical, as discussed in [[Software Inventory Cmdlets]] — the registry-based function remains the more reliable choice for a genuine audit.

---

## ⚠️ Common Mistakes

- Checking only the 64-bit registry path and silently missing every 32-bit application on the machine.
- Not filtering on `DisplayName`, resulting in a report cluttered with unnamed system update entries that aren't genuine applications.
- Running this against many remote machines sequentially instead of leveraging `Invoke-Command`'s built-in support for multiple `-ComputerName` values in parallel, making a fleet-wide audit unnecessarily slow.
- Forgetting per-user installs live under `HKCU`, not `HKLM` — an application installed only for a specific user won't appear in this machine-wide check at all.

---

## 💡 Wisdom from Mímir

Build the registry-based inventory logic once as a reusable function, not as a one-off inline script — it's exactly the kind of small tool that gets needed again and again across unrelated tickets, and having it ready as a named function saves rewriting the same WOW6432Node-aware logic every time.

---

## 🔗 Related Notes

- [[Software Inventory Cmdlets]]
- [[Registry Cmdlets]]
- [[Checking Which Version of an Application Is Installed]]
- [[Silently Uninstalling an Application]]
