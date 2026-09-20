---
type: concept
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Intermediate
tags:
  - powershell-domain-reference
  - software-inventory
aliases:
  - Get-Package
  - Installed Software PowerShell
publish: true
permalink: powershell/software-inventory-cmdlets
---

# <span class="rune">ᛟ</span> Software Inventory Cmdlets

> *There is no single, complete list of "installed software" on Windows — only several partial views, each catching a different installer technology, and real inventory work means checking more than one.*

---

## 🎯 Purpose

Finding out what's installed on a machine sounds simple but has no single authoritative source: MSI-based installs, registry-only installs, and Windows Store/AppX packages are all tracked differently. This concept covers the practical combination of methods — registry uninstall keys (the most complete real-world view) and `Get-Package` — used together to answer "what software is actually on this machine."

---

## 🧠 Key Ideas

- The **registry uninstall keys** (`HKLM:\...\Uninstall\*` and its WOW6432Node 32-bit equivalent, plus `HKCU:\...\Uninstall\*` for per-user installs) are the single most complete source most admins actually rely on — this is exactly what Programs and Features reads from.
- `Get-Package` (from the `PackageManagement`/`OneGet` module) queries multiple providers (MSI, Programs, and others) in one call, but doesn't always catch everything the registry approach does.
- `Get-CimInstance -ClassName Win32_Product` **only** sees MSI-installed software, is notoriously slow, and triggers a Windows Installer reconfiguration as a side effect of being queried — it is widely considered unsuitable for routine inventory work despite being the first thing many people reach for.
- Comparing 32-bit vs. 64-bit registry paths matters: 32-bit applications on a 64-bit OS register under the `WOW6432Node` subkey, and a script checking only the 64-bit path silently misses them.
- Getting a specific application's version reliably usually means reading its uninstall registry entry's `DisplayVersion` property, since not every application exposes version info through `Get-Package` consistently.

---

## ⚙️ How It Works

Windows doesn't maintain one central "installed software" database — Programs and Features itself is really just a friendly view over the registry's uninstall keys, populated by whatever each installer technology chooses to write there when it installs. This is why the registry approach, despite feeling like a workaround, is actually the *most* complete and authoritative method in practice — it's the same data source the built-in Windows UI uses.

```text
HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*        → 64-bit installed apps
HKLM:\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  → 32-bit apps on 64-bit OS
HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*        → per-user installs

Get-Package                → convenient, multi-provider, sometimes incomplete
Win32_Product (CIM)        → MSI-only, slow, has installer side effects — avoid for routine checks
```

---

## 💻 Examples

```powershell
# The reliable, complete method — registry uninstall keys (64-bit + 32-bit-on-64-bit)
$paths = @(
    'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*',
    'HKLM:\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*'
)
Get-ItemProperty $paths |
    Where-Object DisplayName |
    Select-Object DisplayName, DisplayVersion, Publisher, InstallDate |
    Sort-Object DisplayName

# Quicker but sometimes incomplete
Get-Package | Select-Object Name, Version, ProviderName

# Checking whether one specific app is installed and its version
Get-ItemProperty $paths |
    Where-Object {$_.DisplayName -like '*Google Chrome*'} |
    Select-Object DisplayName, DisplayVersion

# AVOID for routine checks — slow, MSI-only, has side effects
# Get-CimInstance -ClassName Win32_Product
```

---

## 🚀 Real World Applications

- Auditing a machine's full software inventory as part of a compliance or licensing check
- Confirming whether a specific application (and which version) is installed before troubleshooting a compatibility issue
- Verifying successful software deployment after an SCCM or Intune push by checking the registry post-install
- Comparing installed software lists across multiple machines to spot configuration drift

---

## ⚖️ Advantages

- The registry-based method requires no special module and reflects exactly what Programs and Features shows, with zero side effects from querying it.
- Covers both machine-wide and per-user installs when both `HKLM` and `HKCU` paths are checked.
- `Get-Package` is convenient for a quick, cross-provider check when completeness isn't critical.
- Neither method requires the target application's own tooling or license to check its presence/version.

---

## ⚠️ Limitations

- No single method is guaranteed 100% complete — some poorly-behaved installers register incompletely or nowhere in the standard locations.
- The registry approach requires remembering to check both the 64-bit and WOW6432Node 32-bit paths, plus `HKCU` for per-user installs — easy to miss one and under-report.
- `Get-Package` provider coverage can vary between Windows versions and what's installed on the specific machine (some providers require their own module to be present).
- AppX/Windows Store apps require an entirely separate cmdlet (`Get-AppxPackage`) — none of the methods above see them.

---

## 🚨 Common Mistakes

- Reaching for `Get-CimInstance -ClassName Win32_Product` out of habit, not realizing it's slow, MSI-only, and can trigger unwanted MSI reconfiguration just by being queried.
- Checking only the 64-bit registry uninstall path and missing every 32-bit application installed on a 64-bit OS via the WOW6432Node path.
- Forgetting per-user installs live under `HKCU`, not `HKLM`, and running the check only under a different user context than the one that actually installed the software.
- Assuming `Get-Package` alone gives a complete inventory, when the registry-based method typically catches more real-world installs.

---

## 📖 Further Reading

- `Get-Help Get-Package -Full`
- `Get-Help Get-AppxPackage -Full`
- Microsoft Learn: "Uninstall registry key reference" (community-documented, not an official `about_` topic)

---

## 💡 Wisdom from Mímir

Skip `Win32_Product` for anything routine — the registry uninstall keys give the same (and usually more complete) answer without the performance cost or the side effect of silently poking every MSI installation on the machine.

---

## 🔗 Related Notes

- [[Finding Installed Software on a Machine]]
- [[Checking Which Version of an Application Is Installed]]
- [[Registry Cmdlets]]
- [[PowerShell Codex]]
