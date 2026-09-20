---
type: powershell-pattern
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Beginner
tags:
  - windows
  - processes
  - troubleshooting
aliases:
  - Stop-Process Force
  - Kill Frozen Application
publish: true
permalink: powershell/finding-and-killing-a-frozen-process
---

# <span class="rune">ᚲ</span> Finding and Killing a Frozen Process

> *"Not Responding" is a specific, checkable fact about a process's message pump — not a vague description. `Responding: $false` is the same observation, just something a script can act on instead of a human squinting at a window title.*

---

## 🎯 Problem

An application has frozen — its window is unresponsive, and the user can't close it normally. Identify the exact process and terminate it cleanly, without guessing which of several similarly-named processes is the actual culprit.

More generally, this pattern answers:
> How do I find a specific unresponsive process and force it to close, confirming afterward that it's actually gone?

---

## 🤔 Mental Model

Think of `Responding` as asking a process "are you still listening?" and timing how long it takes to answer. A healthy process answers instantly. A frozen one never answers at all — its message pump is stuck, which is exactly what Windows itself uses to paint that "(Not Responding)" text in the title bar. Checking `.Responding` in PowerShell is asking the same question programmatically, rather than relying on a human noticing the window title changed.

---

## 🧠 Why This Pattern Works

Every `Process` object returned by `Get-Process` carries a `Responding` boolean property, reflecting whether the process's main window is currently responding to Windows messages — the same underlying signal that drives the "(Not Responding)" label in the title bar. `Stop-Process -Force` sends a termination request directly to the process at the OS level, bypassing any graceful shutdown the application itself might otherwise attempt (and would be exactly why it's stuck in the first place) — as covered generally in [[Process and Service Cmdlets]].

---

## 💻 PowerShell Solution

```powershell
# Find all processes currently not responding
Get-Process | Where-Object { $_.Responding -eq $false }

# Confirm the specific process by name before killing it
Get-Process -Name 'notepad' | Select-Object Id, Name, Responding, CPU, WorkingSet

# Force-kill it
Stop-Process -Name 'notepad' -Force

# Confirm it's actually gone
Get-Process -Name 'notepad' -ErrorAction SilentlyContinue
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Identify unresponsive processes

```powershell
Get-Process | Where-Object { $_.Responding -eq $false }
```

Surfaces every currently-frozen process system-wide, useful when the specific application isn't already known.

### Step 2 — Confirm the correct target before acting

```powershell
Get-Process -Name 'notepad' | Select-Object Id, Name, Responding, CPU, WorkingSet
```

Especially important when multiple instances of similarly-named processes are running — checking `Id` and resource usage first avoids killing the wrong instance.

### Step 3 — Force termination and verify

```powershell
Stop-Process -Name 'notepad' -Force
Get-Process -Name 'notepad' -ErrorAction SilentlyContinue
```

The follow-up check confirms the process actually terminated — `Stop-Process` doesn't always throw a visible error if termination is delayed or partially blocked.

---

## 🚀 Common Use Cases

- Force-closing a hung application a user can't close through the normal UI
- Scripted cleanup of known problematic processes as part of a scheduled maintenance task
- Killing a specific process by ID when multiple instances of the same executable are running and only one is misbehaving
- Confirming a process is actually terminated after a remote force-kill via [[Remoting]]

---

## ⚖️ Alternatives

### Killing by process ID for precision when multiple instances exist

```powershell
Stop-Process -Id 4821 -Force
```

Safer than killing by name when several instances of the same executable are running and only a specific one (identified by its `Id`) needs to go.

### Remote force-kill via Invoke-Command

```powershell
Invoke-Command -ComputerName 'ws042' -ScriptBlock {
    Get-Process -Name 'notepad' | Stop-Process -Force
}
```

Applies the same pattern to a remote machine, useful for a help desk resolving a hung application without needing to physically visit the workstation.

### Graceful close attempt before forcing

```powershell
(Get-Process -Name 'notepad').CloseMainWindow()
Start-Sleep -Seconds 3
if (Get-Process -Name 'notepad' -ErrorAction SilentlyContinue) {
    Stop-Process -Name 'notepad' -Force
}
```

`.CloseMainWindow()` requests a graceful shutdown first (giving the application a chance to save unsaved work), falling back to a forced kill only if it doesn't actually close within a short window — a more considerate approach when data loss is a concern.

---

## ⚠️ Common Mistakes

- Killing by name (`-Name`) when multiple instances of the same executable are running, terminating the wrong instance along with (or instead of) the intended one.
- Using `Stop-Process -Force` reflexively as the first step, without trying `.CloseMainWindow()` first, causing unnecessary loss of unsaved application data when a graceful close would have worked.
- Not verifying termination actually succeeded — some processes with pending I/O or driver-level hangs can take a moment (or fail outright) to actually exit even after `Stop-Process -Force` is called.
- Assuming `Responding: $true` means the application is genuinely healthy — a process can still respond to window messages while being functionally broken in other ways (a stuck background thread, for instance).

---

## 💡 Wisdom from Mímir

Reach for `.CloseMainWindow()` before `Stop-Process -Force` whenever the process might be holding unsaved user work — the few seconds spent attempting a graceful close is cheap insurance against needlessly discarding data a forced kill would have thrown away.

---

## 🔗 Related Notes

- [[Process and Service Cmdlets]]
- [[Finding Which Process Is Locking a File]]
- [[Finding the Top CPU or Memory-Consuming Processes]]
- [[Restarting a Hung Windows Service]]
