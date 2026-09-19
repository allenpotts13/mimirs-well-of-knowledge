---
type: powershell-pattern
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Advanced
tags:
  - applications
  - file-locks
  - handles
aliases:
  - Handle.exe PowerShell
  - Cannot Delete File In Use
publish: true
permalink: powershell/finding-which-process-is-locking-a-file
---

# <span class="rune">ᚲ</span> Finding Which Process Is Locking a File

> *"This file is open in another program" would be a lot more useful if Windows just said which program — Sysinternals Handle does exactly that, because native PowerShell alone genuinely can't.*

---

## 🎯 Problem

A file can't be deleted, moved, or overwritten because it's "in use by another program" — but Windows doesn't say which one, and closing every open application to find out by trial and error isn't practical.

More generally, this pattern answers:
> Which specific process currently holds a lock on this file, so I can decide whether to close it or wait?

---

## 🧠 Why This Pattern Works

Windows deliberately does not expose file-handle-to-process mapping through any native PowerShell cmdlet — this information lives deep in kernel handle tables that require specialized tooling to enumerate safely. Sysinternals' `handle.exe` (a free, long-standing Microsoft-owned utility) does exactly this, and wrapping its output in PowerShell turns a manual troubleshooting tool into something scriptable and reusable — there's no native `Get-*` cmdlet equivalent as of current PowerShell versions.

---

## 💻 PowerShell Solution

```powershell
# Requires handle.exe from Sysinternals (https://learn.microsoft.com/sysinternals/downloads/handle)
# Download once and reference its path, or place it in a folder on $env:PATH

$filePath = 'C:\Shared\report.xlsx'
$handleOutput = & 'C:\Tools\handle.exe' -accepteula $filePath 2>$null

$handleOutput | Where-Object { $_ -match 'pid:\s+(\d+)' } | ForEach-Object {
    if ($_ -match '^(?<proc>\S+)\s+pid:\s+(?<pid>\d+)\s+type:\s+\S+\s+(?<handle>\S+):\s+(?<path>.+)$') {
        [pscustomobject]@{
            ProcessName = $Matches.proc
            ProcessId   = $Matches.pid
            FilePath    = $Matches.path
        }
    }
}
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Run handle.exe against the specific locked file

```powershell
& 'C:\Tools\handle.exe' -accepteula $filePath
```

`-accepteula` suppresses the interactive license prompt on first run, essential for unattended/scripted use.

### Step 2 — Parse its text output into structured results

```powershell
if ($_ -match '^(?<proc>\S+)\s+pid:\s+(?<pid>\d+)\s+type:\s+\S+\s+(?<handle>\S+):\s+(?<path>.+)$')
```

Like `quser` in [[User and Session Cmdlets]], `handle.exe` predates PowerShell's object model and returns plain text — a regex with named capture groups turns it into something a script can act on directly.

### Step 3 — Act on the identified process

```powershell
Stop-Process -Id $result.ProcessId -Force   # only after confirming it's safe to close
```

Once the actual locking process is known, the resolution becomes a normal [[Process and Service Cmdlets|process management]] decision — close it gracefully, force-kill it, or simply wait for it to finish, depending on what's appropriate.

---

## 🚀 Common Use Cases

- Diagnosing "cannot delete, file is open in another program" errors without guessing which application to close
- Identifying a runaway or orphaned process still holding a lock on a file long after its parent application appears closed
- Investigating why a scheduled script fails intermittently trying to overwrite a file another process periodically locks
- Confirming a backup or antivirus process is the actual source of a persistent file lock rather than user activity

---

## ⚖️ Alternatives

### Restarting Explorer as a first, low-effort attempt (when the lock is UI-related)

```powershell
Stop-Process -Name explorer -Force
```

Sometimes a stuck File Explorer preview handle is the actual culprit — worth trying before deeper investigation, since it's non-destructive to any real application data.

### Using Sysinternals Process Explorer's built-in "Find Handle or DLL" GUI feature

For interactive, one-off investigation on a machine with direct access, Process Explorer's `Ctrl+F` search is a GUI-based equivalent — `handle.exe` remains the better choice specifically because it's scriptable and works headlessly against remote sessions.

---

## ⚠️ Common Mistakes

- Force-killing the identified process without confirming what it actually is or what work it might be doing — a legitimate backup or antivirus scan holding a temporary lock is very different from a genuinely stuck application.
- Not running the script elevated — `handle.exe` often needs administrative rights to see handles owned by other users' processes, and running unelevated can produce an incomplete or empty result that looks like "nothing is locking this file" when something actually is.
- Assuming `handle.exe`'s output format is stable across all versions — parsing logic built against one version's exact text layout may need adjustment after a Sysinternals update changes formatting.
- Downloading `handle.exe` from an unofficial mirror instead of the official Microsoft Sysinternals site — always verify the source of any external tool before running it with elevated privileges.

---

## 📖 Further Reading

- Microsoft Learn / Sysinternals: "Handle" utility documentation and download

---

## 💡 Wisdom from Mímir

Before assuming a file lock is a mystery, remember Windows simply doesn't expose this information natively — reaching for Sysinternals `handle.exe` isn't a workaround, it's the actual standard tool for a question PowerShell alone was never built to answer directly.

---

## 🔗 Related Notes

- [[Process and Service Cmdlets]]
- [[Finding and Killing a Frozen Process]]
- [[User and Session Cmdlets]]
- [[Identifying What's Filling Up a Disk]]
