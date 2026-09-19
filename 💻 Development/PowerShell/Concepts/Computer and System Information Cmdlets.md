---
type: concept
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Beginner
tags:
  - powershell-domain-reference
  - system-information
aliases:
  - Get-ComputerInfo
  - System Information PowerShell
publish: true
permalink: powershell/computer-and-system-information-cmdlets
---

# <span class="rune">ᛟ</span> Computer and System Information Cmdlets

> *The first question in almost every troubleshooting session — what is this machine, what's it running, how long has it been up — has a direct cmdlet answer, if you know which one to reach for.*

---

## 🎯 Purpose

A cluster of cmdlets exists specifically to answer "tell me about this machine": OS version and build, uptime, environment variables, hostname, and hardware summary. These are usually the very first commands run at the start of any troubleshooting session, establishing the baseline facts before digging into a specific problem.

Several of these overlap in what they report — knowing which one is fastest or most complete for a given fact avoids reaching for an overly heavy cmdlet when a lighter one would do.

---

## 🧠 Key Ideas

- `Get-ComputerInfo` is the broadest single cmdlet — returns dozens of properties covering OS, BIOS, hardware, and hotfix summary in one call, but is noticeably slower than more targeted alternatives.
- `$env:COMPUTERNAME`, `[System.Environment]::MachineName`, and `hostname` (the external command) all return the local machine name — `$env:COMPUTERNAME` is the fastest and most idiomatic in scripts.
- `Get-CimInstance -ClassName Win32_OperatingSystem` gives OS build, install date, and (via `.LastBootUpTime`) the basis for calculating uptime.
- `(Get-Date) - (Get-CimInstance Win32_OperatingSystem).LastBootUpTime` is the standard pattern for computing exact system uptime as a `TimeSpan`.
- `Get-Item Env:` or `Get-ChildItem Env:` lists every environment variable — useful for confirming `PATH`, `TEMP`, or application-specific variables during troubleshooting.

---

## ⚙️ How It Works

`Get-ComputerInfo` internally gathers information from multiple sources (registry, CIM, WMI) and assembles it into one large object — convenient for a full snapshot, but overkill and measurably slower when only one or two specific facts are needed. For targeted questions, going directly to the underlying [[CIM and WMI|CIM class]] (`Win32_OperatingSystem`, `Win32_ComputerSystem`, `Win32_BIOS`) or a built-in automatic variable (`$env:COMPUTERNAME`) is faster and more explicit about exactly what's being retrieved.

Uptime specifically isn't returned directly by any cmdlet — it's *calculated* by subtracting `LastBootUpTime` (a `DateTime` property on `Win32_OperatingSystem`) from the current time, producing a `TimeSpan` object with `.Days`, `.Hours`, and `.Minutes` properties.

```text
Get-CimInstance Win32_OperatingSystem  →  .LastBootUpTime (a DateTime)
(Get-Date) - .LastBootUpTime            →  TimeSpan (the actual "uptime")
```

---

## 💻 Examples

```powershell
# Full system snapshot (slower, comprehensive)
Get-ComputerInfo

# Fast, targeted machine name
$env:COMPUTERNAME

# OS build and version
Get-CimInstance -ClassName Win32_OperatingSystem |
    Select-Object Caption, Version, BuildNumber, OSArchitecture

# Calculating uptime
$os = Get-CimInstance -ClassName Win32_OperatingSystem
(Get-Date) - $os.LastBootUpTime

# All environment variables
Get-ChildItem Env: | Sort-Object Name

# Domain/workgroup membership and manufacturer/model
Get-CimInstance -ClassName Win32_ComputerSystem |
    Select-Object Manufacturer, Model, Domain, PartOfDomain
```

---

## 🚀 Real World Applications

- Confirming exact OS build/version before troubleshooting an update-specific issue
- Calculating uptime to confirm whether a pending reboot was actually applied
- Checking `Win32_ComputerSystem` domain membership when diagnosing GPO or authentication issues
- Pulling manufacturer/model as the first step of a hardware compatibility check

---

## ⚖️ Advantages

- Covers the "establish the baseline" step of troubleshooting with a handful of memorable, well-documented cmdlets.
- Targeted CIM queries are fast enough to run repeatedly across many remote machines without noticeable delay.
- Uptime calculation via `LastBootUpTime` is precise to the second, unlike some GUI tools that round to the nearest day.
- Environment variable inspection via `Env:` requires no special module or permissions.

---

## ⚠️ Limitations

- `Get-ComputerInfo` is measurably slow — not the right choice inside a loop querying many remote machines for just one or two facts.
- `Get-ComputerInfo` is Windows PowerShell/PowerShell 7-specific behavior can vary slightly between versions in which properties are populated.
- Uptime via `LastBootUpTime` reflects the last full boot — it won't reflect a fast startup/hybrid shutdown state accurately on some Windows configurations, which can quietly under-report actual "since last full boot" time.
- Environment variables retrieved via `Env:` reflect the current process's environment, which may differ from a variable set at the system or user level if the session was started before that change.

---

## 🚨 Common Mistakes

- Reaching for `Get-ComputerInfo` inside a loop over many remote computers, when a single targeted CIM property would return in a fraction of the time across the whole set.
- Reading `LastBootUpTime` itself and mistaking it for "uptime" — it's a point-in-time timestamp, not a duration; the subtraction step is required.
- Assuming `$env:COMPUTERNAME` always matches the DNS hostname — on multi-homed or renamed machines these can occasionally diverge.
- Forgetting fast startup (hybrid boot) on client Windows editions can make `LastBootUpTime` misleading after what looked like a full shutdown/restart cycle.

---

## 📖 Further Reading

- `Get-Help Get-ComputerInfo -Full`
- `Get-Help about_Environment_Variables`
- Microsoft Learn: `Win32_OperatingSystem` and `Win32_ComputerSystem` class references

---

## 💡 Wisdom from Mímir

Reach for `Get-ComputerInfo` when you want everything and speed doesn't matter; reach for a targeted `Get-CimInstance` call when you know exactly what fact you need and might be asking dozens of machines the same question.

---

## 🔗 Related Notes

- [[CIM and WMI]]
- [[Checking System Uptime and Last Boot Time]]
- [[Retrieving BIOS and System Information]]
- [[PowerShell Codex]]
