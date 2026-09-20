---
type: concept
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Intermediate
tags:
  - powershell-domain-reference
  - registry
aliases:
  - Get-ItemProperty
  - PowerShell Registry Access
publish: true
permalink: powershell/registry-cmdlets
---

# <span class="rune">ᛟ</span> Registry Cmdlets

> *PowerShell doesn't have registry-specific cmdlets at all — it treats the registry as just another drive, and the same `Get-Item`/`Set-Item` vocabulary you'd use on the filesystem works on it unchanged.*

---

## 🎯 Purpose

PowerShell exposes the Windows registry through its **provider** model as navigable drives (`HKLM:`, `HKCU:`), meaning ordinary item cmdlets — `Get-Item`, `Get-ItemProperty`, `Set-ItemProperty`, `New-Item`, `Remove-Item` — work on registry keys and values exactly as they do on files and folders. There's no separate "registry module" to learn; the skill is understanding the mapping between filesystem-style operations and registry concepts (keys as containers, values/properties as their contents).

---

## 🧠 Key Ideas

- Registry **keys** behave like folders — navigate them with `Get-ChildItem`, `Set-Location`, `Test-Path`, exactly like a filesystem path.
- Registry **values** (what actually holds data inside a key) behave like a folder's *properties*, not its children — retrieved with `Get-ItemProperty`, not `Get-ChildItem`.
- `HKLM:` and `HKCU:` are pre-mounted PSDrives for `HKEY_LOCAL_MACHINE` and `HKEY_CURRENT_USER`; other hives (`HKEY_USERS`, `HKEY_CLASSES_ROOT`) require mounting manually with `New-PSDrive` if needed.
- `Set-ItemProperty -Path <key> -Name <value name> -Value <data>` writes registry values; `New-ItemProperty` explicitly creates a new one with a specified type (`String`, `DWord`, `Binary`, etc.).
- `Test-Path` works on registry paths exactly like file paths — the standard way to check whether a key exists before trying to read or write to it.

---

## ⚙️ How It Works

PowerShell's provider architecture abstracts different data stores — filesystem, registry, certificate store, environment variables — behind the same small set of "item" cmdlets. The registry provider maps `HKLM:\SOFTWARE\...` paths onto real registry hives and keys, so `Get-ChildItem HKLM:\SOFTWARE\Microsoft` lists subkeys exactly the way it would list subfolders on a disk drive. The one conceptual seam is that a registry key's actual *data* (its values) aren't its children — they're properties on the key itself, which is why listing a key's values requires `Get-ItemProperty`, not `Get-ChildItem`.

```text
Get-ChildItem HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion   → lists SUBKEYS (like folders)
Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion → lists VALUES within that key (like file contents)
```

---

## 💻 Examples

```powershell
# Navigate the registry like a filesystem
Get-ChildItem HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion

# Read values within a specific key
Get-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion' |
    Select-Object ProductName, CurrentBuild, ReleaseId

# Check whether a key exists before acting
Test-Path 'HKLM:\SOFTWARE\Contoso\App'

# Create a new key
New-Item -Path 'HKLM:\SOFTWARE\Contoso\App' -Force

# Set (or create) a specific registry value
New-ItemProperty -Path 'HKLM:\SOFTWARE\Contoso\App' -Name 'Version' -Value '1.2.3' -PropertyType String -Force

# Update an existing value
Set-ItemProperty -Path 'HKLM:\SOFTWARE\Contoso\App' -Name 'Version' -Value '1.2.4'

# Remove a value or an entire key
Remove-ItemProperty -Path 'HKLM:\SOFTWARE\Contoso\App' -Name 'Version'
Remove-Item -Path 'HKLM:\SOFTWARE\Contoso\App' -Recurse
```

---

## 🚀 Real World Applications

- Checking installed OS build/edition detail directly from `CurrentVersion` registry keys during troubleshooting
- Reading/writing application configuration stored in the registry as part of a deployment or remediation script
- Verifying or applying a specific security/group-policy-related registry setting during hardening work
- Cross-referencing the [[Software Inventory Cmdlets|uninstall registry keys]] for software inventory, which is itself just this same provider model applied to a specific well-known path

---

## ⚖️ Advantages

- No separate module or cmdlet vocabulary to learn — filesystem skills transfer directly to registry work.
- `Test-Path`/`Get-ItemProperty`/`Set-ItemProperty` compose naturally with everything else in [[The Pipeline|the pipeline]] and [[Error Handling|error handling]] patterns already used elsewhere.
- Remote registry access works through the same [[Remoting|remoting]] mechanisms as any other remote command, wrapped in `Invoke-Command`.
- Explicit `-PropertyType` on `New-ItemProperty` avoids the ambiguity of manually editing the registry through `regedit`, where type selection is a separate, easy-to-miss step.

---

## ⚠️ Limitations

- Registry writes to `HKLM:` require an elevated session — a very common source of "access denied" errors for scripts not run as administrator.
- The key-vs-value distinction (`Get-ChildItem` for keys, `Get-ItemProperty` for values) is a conceptual seam that trips up anyone expecting registry values to behave like child items.
- Directly modifying the registry carries real risk — a wrong key or value can affect system stability, and there's no built-in undo beyond restoring from a backup.
- Some registry data (especially security-sensitive hives like SAM) isn't accessible even to administrators without additional privilege elevation (`SeBackupPrivilege`, etc.).

---

## 🚨 Common Mistakes

- Using `Get-ChildItem` expecting to see a key's *values*, when it only shows *subkeys* — `Get-ItemProperty` is the correct cmdlet for reading values.
- Forgetting registry writes to `HKLM:` need an elevated (Run as Administrator) session, and getting an access-denied error that looks unrelated to permissions at first glance.
- Omitting `-PropertyType` on `New-ItemProperty`, causing PowerShell to guess the type from the value passed in — sometimes producing a `String` when a `DWord` was actually intended.
- Editing a registry key directly during troubleshooting without exporting/backing it up first, with no easy way to revert if the change causes unexpected side effects.

---

## 📖 Further Reading

- `Get-Help about_Registry_Provider`
- `Get-Help Get-ItemProperty -Full`
- `Get-Help about_Providers`

---

## 💡 Wisdom from Mímir

Before writing any registry value in a script meant to run unattended, add a `Test-Path` check and export/backup step first — the registry provider makes editing the registry as easy as editing a text file, but it doesn't add any of a text file's forgiveness for mistakes.

---

## 🔗 Related Notes

- [[Software Inventory Cmdlets]]
- [[PowerShell Security Fundamentals]]
- [[Error Handling]]
- [[PowerShell Codex]]
