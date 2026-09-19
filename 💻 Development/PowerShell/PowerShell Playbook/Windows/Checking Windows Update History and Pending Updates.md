---
type: powershell-pattern
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Intermediate
tags:
  - windows
  - windows-update
  - patching
aliases:
  - Get-HotFix
  - PSWindowsUpdate
publish: true
permalink: powershell/checking-windows-update-history-and-pending-updates
---

# <span class="rune">ᚲ</span> Checking Windows Update History and Pending Updates

> *A machine "isn't updating" is really three separate questions — what's already installed, what's actually pending, and what's the actual state of the update service — and each one needs a different check.*

---

## 🎯 Problem

A machine hasn't received a known update, or a user is asking whether a specific patch is installed after a reported vulnerability. Confirm exactly what's installed, and what's still pending.

More generally, this pattern answers:
> What updates has this machine already installed, and what's currently pending or failing to install?

---

## 🤔 Mental Model

Checking Windows Update is like checking three separate gauges on the same dashboard: what's already been installed (the history), what's queued up waiting (pending), and whether the mechanism itself is even running correctly (the Windows Update service state). A machine can look "broken" from one gauge while the other two are perfectly fine — treating this as one single question instead of three is the most common way to misdiagnose an update problem.

---

## 🧠 Why This Pattern Works

`Get-HotFix` reads the same installed-update data as `wmic qfe` used to, reflecting what's actually been applied to the OS — but it only covers a subset of update types (primarily older-style QFE/hotfix packages, not the full range of modern cumulative updates and feature updates). For a genuinely complete picture — especially pending updates that haven't installed yet — the community `PSWindowsUpdate` module wraps the actual Windows Update Agent API directly, giving visibility the built-in cmdlets alone don't provide.

---

## 💻 PowerShell Solution

```powershell
# Built-in — installed hotfixes/updates (partial coverage, but always available)
Get-HotFix | Sort-Object InstalledOn -Descending | Select-Object -First 10

# Check whether a SPECIFIC KB is installed
Get-HotFix -Id 'KB5034441' -ErrorAction SilentlyContinue

# PSWindowsUpdate module — full pending/available update visibility (requires installation)
Install-Module -Name PSWindowsUpdate -Scope CurrentUser -Force
Import-Module PSWindowsUpdate

Get-WindowsUpdate                     # what's currently available/pending
Get-WUHistory | Select-Object -First 10   # fuller installation history than Get-HotFix

# Trigger a scan and install pending updates
Install-WindowsUpdate -AcceptAll -AutoReboot:$false
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Check what's already installed (built-in, no extra module needed)

```powershell
Get-HotFix | Sort-Object InstalledOn -Descending | Select-Object -First 10
```

Quick and universally available, but incomplete — modern cumulative updates aren't always fully represented here the way `PSWindowsUpdate`'s history is.

### Step 2 — Check for a specific known patch

```powershell
Get-HotFix -Id 'KB5034441' -ErrorAction SilentlyContinue
```

Directly answers "is this specific security patch installed" — the most common real-world version of this question, especially after a vulnerability disclosure names a specific KB.

### Step 3 — For full pending/available update visibility, use PSWindowsUpdate

```powershell
Get-WindowsUpdate
```

Queries the actual Windows Update Agent for what's currently detected as available but not yet installed — information `Get-HotFix` alone cannot provide, since it only reports what's already been applied.

---

## 🚀 Common Use Cases

- Confirming whether a specific security patch is installed after a vulnerability disclosure names a CVE/KB
- Investigating why a machine appears to be falling behind on patching compared to the rest of the fleet
- Triggering an update scan and install remotely as part of a manual remediation when automatic updates have stalled
- Building a fleet-wide report of patch compliance across many machines via [[Remoting]]

---

## ⚖️ Alternatives

### Checking Windows Update service health first, before assuming a patching problem

```powershell
Get-Service -Name wuauserv, bits | Select-Object Name, Status, StartType
```

If the Windows Update service (`wuauserv`) or Background Intelligent Transfer Service (`bits`) isn't running, no update mechanism will function at all — worth ruling this out before investigating specific patches.

### Remote fleet-wide compliance check

```powershell
Invoke-Command -ComputerName $servers -ScriptBlock {
    Get-HotFix -Id 'KB5034441' -ErrorAction SilentlyContinue
} | Select-Object PSComputerName, HotFixID, InstalledOn
```

Scales the specific-KB check across many machines at once, quickly identifying which ones are missing a critical patch.

---

## ⚠️ Common Mistakes

- Relying on `Get-HotFix` alone to conclude a machine is "up to date," when it doesn't fully represent modern cumulative update history — a missing entry there doesn't always mean the update wasn't actually applied.
- Not checking the `wuauserv`/`bits` service state first when updates appear stuck — a stopped or misconfigured update service will block all patching regardless of any other troubleshooting.
- Installing `PSWindowsUpdate` and running `Install-WindowsUpdate -AutoReboot:$true` unattended on a production server without coordinating the reboot timing with users or other maintenance windows.
- Assuming a KB number search will always match — some updates supersede or roll up others under a different KB number over time, so "missing" one specific KB doesn't always mean the underlying fix is absent.

---

## 📖 Further Reading

- `Get-Help Get-HotFix -Full`
- PSWindowsUpdate module documentation (PowerShell Gallery)

---

## 💡 Wisdom from Mímir

Before troubleshooting "why won't this update install," check whether Windows Update's own services are even running — a surprising number of patching tickets turn out to be a stopped `wuauserv` or `bits` service, not anything wrong with the update itself.

---

## 🔗 Related Notes

- [[Modules]]
- [[Reading the Last N System Errors from the Event Log]]
- [[Finding Stale or Inactive AD Computer Accounts]]
- [[Rebooting a Remote Computer Safely]]
