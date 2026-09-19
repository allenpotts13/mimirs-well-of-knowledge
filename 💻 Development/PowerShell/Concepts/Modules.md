---
type: concept
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Intermediate
tags:
  - powershell-fundamentals
  - modules
  - import-module
aliases:
  - PowerShell Modules
  - Import-Module
publish: true
permalink: powershell/modules
---

# <span class="rune">ᛟ</span> Modules

> *Every domain-specific cmdlet you've ever used — AD, Exchange, Graph, SCCM — arrived the same way: as a module someone imported before the commands existed.*

---

## 🎯 Purpose

A module is a packaged, shareable unit of PowerShell functionality — functions, cmdlets, variables, and aliases bundled together and loaded on demand with `Import-Module`. Nearly every domain this Codex covers (Active Directory, Microsoft Graph, SCCM) is delivered as a module; none of those commands exist in a fresh PowerShell session until the relevant module is imported.

Understanding modules is what separates "I don't have that cmdlet" from "I haven't imported the module that provides it" — a distinction that resolves a huge share of early PowerShell confusion.

---

## 🧠 Key Ideas

- `Import-Module <name>` loads a module's commands into the current session; `Get-Module -ListAvailable` shows every module installed on the machine, imported or not.
- Modern PowerShell **auto-loads** modules the first time you call one of their commands — but only if the module is installed somewhere on `$env:PSModulePath` and its manifest is discoverable; this doesn't help if the module was never installed at all.
- `Install-Module` (from the PowerShell Gallery, via `PowerShellGet`) downloads and installs a module for the current user or all users — distinct from `Import-Module`, which only loads a module that's already installed.
- A module manifest (`.psd1` file) declares metadata: version, required PowerShell version, exported functions, and dependencies.
- `Get-Command -Module <name>` lists every cmdlet/function a specific module actually provides — the fastest way to discover what's available after importing something new.

---

## ⚙️ How It Works

When PowerShell starts, it doesn't load every installed module — that would make every session slow to start. Instead, it checks `$env:PSModulePath` (a list of folders) for module manifests and registers what's *available* without actually loading the code. The first time a command from an available-but-unloaded module is called, PowerShell auto-imports it transparently — which is why `Get-ADUser` can "just work" the first time it's typed, even without an explicit `Import-Module ActiveDirectory` beforehand, as long as the RSAT AD module is actually installed on that machine.

This auto-loading is also the source of a very common confusion: a command failing with "not recognized" doesn't necessarily mean a typo — it often means the module that provides it was never *installed* in the first place (distinct from simply not being *imported*), which auto-loading cannot fix.

```text
Get-ADUser typed
      ↓
Is ActiveDirectory module already imported? No.
      ↓
Is it available on $env:PSModulePath? 
   Yes → auto-import it, then run Get-ADUser
   No  → "not recognized as a cmdlet" — needs Install-Module/RSAT install first
```

---

## 💻 Examples

```powershell
# See everything installed but not necessarily loaded yet
Get-Module -ListAvailable

# See what's actually loaded in THIS session right now
Get-Module

# Explicitly import a module (usually unnecessary thanks to auto-loading, but useful in scripts for clarity/reliability)
Import-Module ActiveDirectory

# Install a module from the PowerShell Gallery for the current user
Install-Module -Name Microsoft.Graph -Scope CurrentUser

# See exactly what commands a module provides
Get-Command -Module ActiveDirectory

# Check which module a specific command came from
Get-Command Get-ADUser | Select-Object Source
```

---

## 🚀 Real World Applications

- Importing the `ActiveDirectory` module (via RSAT) before running any AD account-management troubleshooting
- Installing `Microsoft.Graph` or `ExchangeOnlineManagement` to manage Microsoft 365/Entra ID from PowerShell instead of the admin portals
- Installing the `ConfigurationManager` module (from an SCCM console install) to run site-server cmdlets against SCCM
- Using `Get-Command -Module <name>` immediately after installing an unfamiliar module to see what it actually offers, instead of guessing cmdlet names

---

## ⚖️ Advantages

- Auto-loading means most day-to-day use never requires an explicit `Import-Module` call at all.
- Modules cleanly separate "commands available in a vanilla session" from "commands that require specific tooling to be installed," which maps naturally onto real permissions/tooling boundaries (RSAT, Exchange admin, SCCM console).
- The PowerShell Gallery (`Install-Module`) puts thousands of community and Microsoft-published modules a single command away.
- `Get-Command -Module` and `Get-Help -Module` give instant discoverability for any newly installed module, without needing external documentation.

---

## ⚠️ Limitations

- Auto-loading can mask *why* a command doesn't exist — it looks the same ("not recognized") whether the module simply isn't imported yet or was never installed at all.
- Some modules (particularly older ones, or those requiring elevated permissions) don't support auto-loading reliably and need an explicit `Import-Module`.
- Module version conflicts (an old cached version vs. a newer installed one) can cause a script to silently run against outdated cmdlet behavior.
- Installing modules system-wide (`-Scope AllUsers`) requires elevation and affects every user on the machine, which isn't always desired.

---

## 🚨 Common Mistakes

- Assuming a "cmdlet not recognized" error means a typo, when it actually means the required module (e.g., RSAT's ActiveDirectory module) was never installed on that machine.
- Running `Install-Module` when the actual need was `Import-Module` (module already installed, just not loaded) — or vice versa.
- Forgetting `-Scope CurrentUser` and hitting a permissions error trying to install a module system-wide without an elevated session.
- Not checking `Get-Module` version drift across different machines — a script tested against one module version can behave differently on a machine with an older cached copy.

---

## 📖 Further Reading

- `Get-Help about_Modules`
- `Get-Help Import-Module -Full`
- Microsoft Learn: "PowerShell Gallery" and "Installing RSAT"

---

## 💡 Wisdom from Mímir

When a cmdlet "doesn't exist," don't assume a typo — run `Get-Module -ListAvailable <name>*` first. Most of the time the real answer is "the module isn't installed on this machine," not "the command was mistyped."

---

## 🔗 Related Notes

- [[Active Directory Cmdlets]]
- [[Microsoft 365 and Entra ID Cmdlets]]
- [[Functions]]
- [[PowerShell Codex]]
