---
type: powershell-pattern
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Beginner
tags:
  - performance
  - uptime
  - reboot-history
aliases:
  - Get-Uptime
  - LastBootUpTime PowerShell
publish: true
permalink: powershell/checking-system-uptime-and-last-boot-time
---

# <span class="rune">ᚲ</span> Checking System Uptime and Last Boot Time

> *Uptime is never returned directly by anything — it's always computed, which means the real skill here is subtraction, not memorizing a cmdlet name.*

---

## 🎯 Problem

Confirm how long a machine has been running without a restart — either to check whether a pending update/reboot was actually applied, or because extremely long uptime is itself suspected as a cause of degraded performance.

More generally, this pattern answers:
> How long has this machine been running since its last boot, and when exactly did that boot happen?

---

## 🤔 Mental Model

As introduced generally in [[Computer and System Information Cmdlets]], no cmdlet hands back "uptime" as a ready-made value — every method ultimately relies on capturing a single fixed point in time (`LastBootUpTime`) and subtracting it from right now. Uptime isn't a fact Windows stores directly; it's a fact you calculate fresh, every single time you ask.

---

## 🧠 Why This Pattern Works

`Win32_OperatingSystem.LastBootUpTime` is a `DateTime` property recording exactly when the machine last completed a full boot — subtracting it from `Get-Date` produces a `TimeSpan` object with `.Days`, `.Hours`, and `.Minutes` properties, giving a precise, calculable uptime rather than an approximate or rounded figure a GUI tool might display.

---

## 💻 PowerShell Solution

```powershell
# Precise uptime as a TimeSpan
$os = Get-CimInstance -ClassName Win32_OperatingSystem
$uptime = (Get-Date) - $os.LastBootUpTime
"{0} days, {1} hours, {2} minutes" -f $uptime.Days, $uptime.Hours, $uptime.Minutes

# PowerShell 7+ convenience cmdlet (local machine only)
Get-Uptime

# Exact last boot timestamp
$os.LastBootUpTime

# Checking uptime across a fleet, flagging anything over a threshold (e.g., 30 days)
$computers = 'ws001','ws002','srv01'
Invoke-Command -ComputerName $computers -ScriptBlock {
    $os = Get-CimInstance -ClassName Win32_OperatingSystem
    [pscustomobject]@{
        ComputerName = $env:COMPUTERNAME
        LastBoot     = $os.LastBootUpTime
        UptimeDays   = [math]::Round(((Get-Date) - $os.LastBootUpTime).TotalDays, 1)
    }
} | Where-Object UptimeDays -gt 30
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Get the last boot timestamp

```powershell
$os = Get-CimInstance -ClassName Win32_OperatingSystem
$os.LastBootUpTime
```

The single fixed point every uptime calculation is built from.

### Step 2 — Subtract from the current time

```powershell
$uptime = (Get-Date) - $os.LastBootUpTime
```

Produces a `TimeSpan` object — the actual, precise duration since boot, down to the second.

### Step 3 — Format for readability, or use `Get-Uptime` as a shortcut

```powershell
Get-Uptime
```

PowerShell 7+ includes this as a convenience cmdlet performing the same calculation internally — a quick option for local checks, though it doesn't extend to remote machines the way the CIM-based approach does.

---

## 🚀 Common Use Cases

- Confirming a pending reboot (for an update or configuration change) was actually applied, by checking uptime dropped to a small value afterward
- Flagging servers with excessively long uptime as candidates for a planned maintenance reboot
- Investigating whether accumulated memory leaks or fragmentation correlate with very long uptime on a slow machine
- Establishing exactly when an unexpected reboot happened, as a starting point for correlating with [[Reading the Last N System Errors from the Event Log|event log investigation]]

---

## ⚖️ Alternatives

### Legacy `net statistics` and `systeminfo` equivalents

```powershell
systeminfo | Select-String 'System Boot Time'
```

Still works, but returns plain text requiring parsing — the CIM-based approach is more directly usable in a script.

### Fleet-wide uptime report for maintenance planning

```powershell
Invoke-Command -ComputerName $servers -ScriptBlock {
    [math]::Round(((Get-Date) - (Get-CimInstance Win32_OperatingSystem).LastBootUpTime).TotalDays, 1)
} | ForEach-Object { [pscustomobject]@{ ComputerName = $_.PSComputerName; UptimeDays = $_ } }
```

Useful for identifying servers overdue for a scheduled reboot as part of routine maintenance planning.

---

## ⚠️ Common Mistakes

- Reading `LastBootUpTime` directly and mistaking it for "uptime" itself — it's a fixed timestamp, not a duration; the subtraction step is required to get an actual uptime value.
- Not accounting for Windows fast startup (hybrid shutdown) on client editions, which can make `LastBootUpTime` misleading after what appeared to be a full shutdown/restart cycle, since fast startup doesn't always perform a genuinely full boot.
- Using `Get-Uptime` and expecting it to work remotely — it's a local-machine-only cmdlet; remote checks need the CIM-based calculation instead.
- Assuming very long uptime is automatically a problem — for many well-maintained servers, long uptime with stable performance is entirely normal and not itself evidence of an issue.

---

## 💡 Wisdom from Mímir

Uptime is always a calculation, never a stored fact — internalizing that means never being surprised there's no single "just show me uptime" property on `Win32_OperatingSystem`, and reaching straight for the subtraction instead of hunting for a property that was never going to exist.

---

## 🔗 Related Notes

- [[Computer and System Information Cmdlets]]
- [[Reading the Last N System Errors from the Event Log]]
- [[Rebooting a Remote Computer Safely]]
- [[Checking Windows Update History and Pending Updates]]
