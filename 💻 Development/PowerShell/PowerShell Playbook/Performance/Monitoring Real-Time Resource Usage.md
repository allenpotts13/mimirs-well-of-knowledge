---
type: powershell-pattern
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Intermediate
tags:
  - performance
  - monitoring
  - performance-counters
aliases:
  - Get-Counter
  - Real-Time Performance Monitoring PowerShell
publish: true
permalink: powershell/monitoring-real-time-resource-usage
---

# <span class="rune">ᚲ</span> Monitoring Real-Time Resource Usage

> *A single `Get-Process` snapshot tells you what's happening right now. `Get-Counter` samples repeatedly over time — the difference between a photograph and a heart monitor.*

---

## 🎯 Problem

A performance issue is intermittent — it comes and goes, and a single point-in-time check via [[Finding the Top CPU or Memory-Consuming Processes]] doesn't reliably catch it happening. Watch resource usage continuously over a period of time instead.

More generally, this pattern answers:
> How do I capture CPU, memory, and disk usage over a span of time to catch an intermittent performance problem in the act?

---

## 🤔 Mental Model

Think of `Get-Counter` as attaching a heart monitor rather than taking a single blood pressure reading — it samples the same performance counters Task Manager's own graphs are built from, repeatedly over a defined interval, producing a timeline instead of one isolated snapshot. This is what actually catches an intermittent spike that a single `Get-Process` check, run at the wrong moment, would miss entirely.

---

## 🧠 Why This Pattern Works

`Get-Counter` reads directly from the same Windows Performance Counter infrastructure that Performance Monitor (`perfmon.exe`) and Task Manager's graphs are built on — `-SampleInterval` and `-MaxSamples` control how often and how many times it samples, turning a single instantaneous check into a genuine time-series capture. Counter paths like `\Processor(_Total)\% Processor Time` and `\Memory\Available MBytes` are standardized, well-documented paths into specific, well-defined system metrics.

---

## 💻 PowerShell Solution

```powershell
# Sample overall CPU and available memory every 2 seconds, 30 times (1 minute total)
Get-Counter -Counter '\Processor(_Total)\% Processor Time', '\Memory\Available MBytes' `
    -SampleInterval 2 -MaxSamples 30 |
    ForEach-Object {
        $_.CounterSamples | Select-Object Path, CookedValue, Timestamp
    }

# Continuous monitoring until manually stopped (Ctrl+C)
Get-Counter -Counter '\Processor(_Total)\% Processor Time' -Continuous |
    ForEach-Object {
        $_.CounterSamples | Select-Object Timestamp, CookedValue
    }

# Discovering available counter paths for a specific category
Get-Counter -ListSet 'Processor' | Select-Object -ExpandProperty Counter
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Choose the counter paths relevant to the investigation

```powershell
'\Processor(_Total)\% Processor Time', '\Memory\Available MBytes'
```

Counter paths follow a `\Category(Instance)\Counter` format — `_Total` as the instance aggregates across all CPU cores rather than reporting each individually.

### Step 2 — Sample repeatedly over a defined window

```powershell
-SampleInterval 2 -MaxSamples 30
```

Sampling every 2 seconds for 30 samples captures a full minute of activity — long enough to catch most short-lived spikes without generating an unmanageable amount of data.

### Step 3 — Extract the readable values from each sample

```powershell
$_.CounterSamples | Select-Object Path, CookedValue, Timestamp
```

`CookedValue` is the human-meaningful processed value (e.g., an actual percentage) as opposed to `RawValue`, which is the unprocessed counter data some counter types require additional calculation to interpret correctly.

---

## 🚀 Common Use Cases

- Catching an intermittent CPU or memory spike that a single point-in-time check keeps missing
- Building a before/after performance comparison around a specific change (a config update, a patch, a new application version)
- Correlating a reported "slowness" complaint with an actual measured resource spike at the same timestamp
- Feeding captured performance data into a report or chart for a capacity-planning discussion

---

## ⚖️ Alternatives

### Exporting a Performance Monitor Data Collector Set for longer unattended captures

```powershell
logman create counter MyDataCollector -c "\Processor(_Total)\% Processor Time" "\Memory\Available MBytes" -f csv -o C:\PerfLogs\capture.csv -si 5
logman start MyDataCollector
# ... let it run ...
logman stop MyDataCollector
```

`logman` (a classic Windows command, still usable from PowerShell) is better suited for long unattended captures (hours or days) than `Get-Counter`, which is more naturally suited to shorter, interactive investigation sessions.

### Combining with process-level detail for correlation

```powershell
1..10 | ForEach-Object {
    $cpu = (Get-Counter '\Processor(_Total)\% Processor Time').CounterSamples.CookedValue
    $topProcess = Get-Process | Sort-Object CPU -Descending | Select-Object -First 1 Name
    [pscustomobject]@{ Time = Get-Date; TotalCPU = [math]::Round($cpu,1); TopProcess = $topProcess.Name }
    Start-Sleep -Seconds 5
}
```

Correlates overall system CPU with whichever process happens to be leading at each sample point, useful for identifying which process is actually driving an observed spike.

---

## ⚠️ Common Mistakes

- Running a single `Get-Counter` call without `-SampleInterval`/`-MaxSamples`/`-Continuous`, capturing only one instantaneous reading — no different from a single `Get-Process` snapshot in terms of catching intermittent issues.
- Using `RawValue` instead of `CookedValue` for counters that require processing (like percentage-based ones), producing numbers that look wrong or meaningless without the proper conversion.
- Setting too short a total capture window for a genuinely intermittent issue that only occurs every few hours — matching the sampling window to the actual reported frequency of the problem matters.
- Not discovering exact counter paths first via `Get-Counter -ListSet` and guessing at counter names, which fail outright if the path doesn't match exactly (including capitalization and spacing in some cases).

---

## 📖 Further Reading

- `Get-Help Get-Counter -Full`
- `Get-Help about_PerformanceCounters` (via Microsoft Learn)

---

## 💡 Wisdom from Mímir

For a genuinely intermittent performance complaint, resist the urge to just run `Get-Process` a few times and call it investigated — match the capture window to how often the problem actually occurs. A spike that happens once an hour needs an hour of sampling, not a five-minute spot check.

---

## 🔗 Related Notes

- [[Finding the Top CPU or Memory-Consuming Processes]]
- [[Process and Service Cmdlets]]
- [[Checking System Uptime and Last Boot Time]]
- [[Tracing a Network Path Hop by Hop]]
