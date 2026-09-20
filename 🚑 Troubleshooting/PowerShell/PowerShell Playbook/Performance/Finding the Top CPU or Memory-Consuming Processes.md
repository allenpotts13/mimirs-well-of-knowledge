---
type: powershell-pattern
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Beginner
tags:
  - performance
  - processes
  - resource-usage
aliases:
  - Top CPU Processes PowerShell
  - Sort-Object CPU
publish: true
permalink: powershell/finding-the-top-cpu-or-memory-consuming-processes
---

# <span class="rune">ᚲ</span> Finding the Top CPU or Memory-Consuming Processes

> *A machine running slow has a specific, findable cause almost every time — the trick is knowing that `CPU` and `WorkingSet` measure genuinely different things, and a process that dominates one can be entirely innocent of the other.*

---

## 🎯 Problem

A machine is running slow, and the cause needs to be identified quickly: which process is actually consuming the CPU or memory that's making everything else sluggish.

More generally, this pattern answers:
> Which processes are using the most CPU time or memory right now, sorted so the worst offender is immediately visible?

---

## 🤔 Mental Model

Treat CPU and memory as two separate leaderboards, not one combined score — a process can top the CPU leaderboard (constantly computing) while barely registering on the memory one, and vice versa (holding a huge amount of memory while sitting mostly idle). Sorting by each independently, rather than trying to eyeball both from an unsorted list, is what actually surfaces the real offender in either category.

---

## 🧠 Why This Pattern Works

As covered generally in [[Objects in PowerShell]] and [[Process and Service Cmdlets]], `Get-Process` returns real numeric properties — `CPU` (cumulative processor time in seconds since the process started) and `WorkingSet`/`WS` (current physical memory usage in bytes) — that `Sort-Object` can order directly and meaningfully, unlike a text-based `tasklist` output that would require manual parsing to sort accurately.

---

## 💻 PowerShell Solution

```powershell
# Top 10 CPU consumers
Get-Process | Sort-Object CPU -Descending | Select-Object -First 10 Name, Id, CPU

# Top 10 memory consumers, converted to readable MB
Get-Process | Sort-Object WorkingSet -Descending | Select-Object -First 10 Name, Id,
    @{N='MemoryMB';E={[math]::Round($_.WorkingSet/1MB,1)}}

# Both leaderboards side by side, useful when the cause isn't yet known
$topCpu = Get-Process | Sort-Object CPU -Descending | Select-Object -First 5 Name, CPU
$topMem = Get-Process | Sort-Object WorkingSet -Descending | Select-Object -First 5 Name,
    @{N='MemoryMB';E={[math]::Round($_.WorkingSet/1MB,1)}}
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Sort by CPU for a processor-bound slowdown

```powershell
Get-Process | Sort-Object CPU -Descending | Select-Object -First 10
```

`CPU` here is *cumulative* processor time since the process started, not a live percentage — a long-running process can show high cumulative CPU without currently being the active bottleneck, which is worth keeping in mind when interpreting the result.

### Step 2 — Sort by WorkingSet for a memory-bound slowdown

```powershell
Get-Process | Sort-Object WorkingSet -Descending | Select-Object -First 10
```

`WorkingSet` reflects current physical memory usage — a much more "right now" measurement than cumulative CPU time, making it directly useful for identifying an active memory hog.

### Step 3 — Convert to readable units for reporting

```powershell
@{N='MemoryMB';E={[math]::Round($_.WorkingSet/1MB,1)}}
```

Raw `WorkingSet` bytes are unreadable at a glance — converting to MB (or GB for very large values) makes the report immediately usable.

---

## 🚀 Common Use Cases

- First response to a "my computer is running slow" ticket, quickly identifying the likely cause
- Investigating a memory leak by checking whether one process's `WorkingSet` grows unexpectedly over repeated checks
- Confirming a runaway background process is responsible for sustained high CPU usage
- Building a lightweight scheduled snapshot of top resource consumers for later comparison

---

## ⚖️ Alternatives

### Live, continuously-updating view (interactive, similar to Task Manager)

```powershell
while ($true) {
    Clear-Host
    Get-Process | Sort-Object CPU -Descending | Select-Object -First 10 Name, CPU, WorkingSet
    Start-Sleep -Seconds 2
}
```

Useful for actively watching resource usage change in real time during a live troubleshooting session, as expanded on in [[Monitoring Real-Time Resource Usage]].

### Remote check across a fleet

```powershell
Invoke-Command -ComputerName 'srv01' -ScriptBlock {
    Get-Process | Sort-Object CPU -Descending | Select-Object -First 5 Name, CPU
}
```

Applies the same check to a remote server exhibiting slowness, without needing to log in interactively.

---

## ⚠️ Common Mistakes

- Interpreting `CPU` as a live, current percentage — it's cumulative time in seconds since the process started, and a long-uptime process can top this list without currently doing anything CPU-intensive at all.
- Not converting `WorkingSet` to readable units before presenting a report, leaving raw byte counts that are technically correct but practically unreadable.
- Assuming the single top process is always the sole cause of a slowdown — sometimes the real issue is many moderate-usage processes collectively, rather than one dominant outlier.
- Checking only one leaderboard (CPU or memory) when the actual complaint doesn't specify which resource is the bottleneck — checking both takes seconds and avoids missing the real cause.

---

## 💡 Wisdom from Mímir

Never assume the answer lies in only one leaderboard — pull both the CPU and memory top-10 lists side by side on the first pass. It costs nothing extra and immediately rules out (or confirms) half the possible causes of a reported slowdown.

---

## 🔗 Related Notes

- [[Process and Service Cmdlets]]
- [[Monitoring Real-Time Resource Usage]]
- [[Finding and Killing a Frozen Process]]
- [[Identifying What's Filling Up a Disk]]
