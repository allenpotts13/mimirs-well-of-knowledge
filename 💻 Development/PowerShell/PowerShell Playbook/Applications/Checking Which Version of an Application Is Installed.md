---
type: powershell-pattern
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Beginner
tags:
  - applications
  - version-check
  - troubleshooting
aliases:
  - Get-ItemProperty DisplayVersion
  - Check Application Version PowerShell
publish: true
permalink: powershell/checking-which-version-of-an-application-is-installed
---

# <span class="rune">ᚲ</span> Checking Which Version of an Application Is Installed

> *A version number lives in more than one place — the registry, the executable's own file properties, and sometimes only inside the application's UI — and a script checking the wrong one will confidently report a wrong answer.*

---

## 🎯 Problem

Confirm exactly which version of an application is installed on a machine — needed before troubleshooting a compatibility issue, or to verify a deployment actually delivered the expected version.

More generally, this pattern answers:
> What version of a specific application is currently installed, and how do I check when the registry entry alone isn't reliable?

---

## 🤔 Mental Model

Think of version information as living on two independent labels: the box the software came in (the registry's `DisplayVersion`, written once at install time) and the product itself (the executable file's own embedded version metadata). Usually these two labels agree — but an in-place update, a manual file replacement, or a poorly-behaved installer can leave them out of sync, which is exactly why checking the actual file's version is sometimes the more trustworthy answer.

---

## 🧠 Why This Pattern Works

The registry's `DisplayVersion` property (from the same uninstall keys covered in [[Software Inventory Cmdlets]]) is set by the installer at install time and reflects what the installer *believes* it deployed — convenient and fast to check, but not always kept current if a file gets patched or replaced outside a formal reinstall. Every Windows executable also carries embedded version metadata (`FileVersion`, `ProductVersion`) directly in the binary itself, retrievable via `.VersionInfo` on a `System.IO.FileInfo` object — this reflects the literal, currently-running file, independent of whatever the registry happens to say.

---

## 💻 PowerShell Solution

```powershell
# Registry-based check (fast, matches Programs and Features)
$paths = @(
    'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*',
    'HKLM:\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*'
)
Get-ItemProperty $paths |
    Where-Object DisplayName -like '*Contoso Client*' |
    Select-Object DisplayName, DisplayVersion

# File-based check (the actual binary's real version — more trustworthy in edge cases)
$exePath = 'C:\Program Files\Contoso\ContosoClient.exe'
(Get-Item $exePath).VersionInfo | Select-Object FileVersion, ProductVersion

# Cross-checking both, and flagging a mismatch
$registryVersion = (Get-ItemProperty $paths | Where-Object DisplayName -like '*Contoso Client*').DisplayVersion
$fileVersion = (Get-Item $exePath).VersionInfo.FileVersion
if ($registryVersion -ne $fileVersion) {
    Write-Warning "Version mismatch: registry says $registryVersion, file says $fileVersion"
}
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Check the registry first (fast, matches what most tools report)

```powershell
Get-ItemProperty $paths | Where-Object DisplayName -like '*Contoso Client*' | Select DisplayVersion
```

This is what Programs and Features itself displays — the right first check for most routine version questions.

### Step 2 — Fall back to the actual file's embedded version when in doubt

```powershell
(Get-Item $exePath).VersionInfo | Select-Object FileVersion, ProductVersion
```

Useful specifically when troubleshooting a suspected mismatch — e.g., a user reports a bug already fixed in a newer version, but the registry still shows the old one.

### Step 3 — Cross-check both when accuracy genuinely matters

```powershell
if ($registryVersion -ne $fileVersion) { Write-Warning "Version mismatch..." }
```

A mismatch itself is useful diagnostic information — it points toward an incomplete update, a manually replaced file, or an installer that didn't fully register its changes.

---

## 🚀 Common Use Cases

- Confirming a deployed application update actually landed on a specific machine
- Diagnosing "this bug should already be fixed" reports by checking the real installed version against a known fix version
- Verifying version compliance across a fleet before a compatibility-sensitive rollout
- Distinguishing a genuinely outdated installation from a registry/file mismatch caused by an incomplete update

---

## ⚖️ Alternatives

### Checking version via Get-Package

```powershell
Get-Package -Name '*Contoso Client*' | Select-Object Name, Version
```

Convenient when the application is registered with a `PackageManagement` provider, though — as noted in [[Software Inventory Cmdlets]] — coverage isn't universal across every installed application.

### Fleet-wide version compliance check

```powershell
Invoke-Command -ComputerName $computers -ScriptBlock {
    (Get-ItemProperty 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*' |
        Where-Object DisplayName -like '*Contoso Client*').DisplayVersion
} | Select-Object PSComputerName, ScriptBlockOutput
```

Scales the registry-based check across an entire fleet to identify which machines are running an outdated version.

---

## ⚠️ Common Mistakes

- Trusting the registry's `DisplayVersion` unconditionally when troubleshooting a specific bug report, without considering it might be stale relative to the actual running file.
- Checking `FileVersion` when `ProductVersion` was the more relevant field (or vice versa) — these can legitimately differ for the same file, since `FileVersion` tracks the specific build while `ProductVersion` tracks the overall product release.
- Assuming a hardcoded file path for the executable is stable across all installations — some applications install to different paths depending on install options or OS architecture.
- Not accounting for per-user installations, where the relevant registry entry lives under `HKCU` rather than `HKLM`, causing a machine-wide check to miss a per-user-installed version entirely.

---

## 💡 Wisdom from Mímir

When a version check "doesn't match what the user is seeing," don't just re-run the same registry query more carefully — check the actual executable's embedded version instead. The mismatch itself, once found, is often the real answer to why the reported behavior doesn't match what should be installed.

---

## 🔗 Related Notes

- [[Software Inventory Cmdlets]]
- [[Finding Installed Software on a Machine]]
- [[Silently Uninstalling an Application]]
- [[Checking a User's Current Logon Session]]
