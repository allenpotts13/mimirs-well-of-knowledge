---
type: powershell-pattern
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Intermediate
tags:
  - performance
  - disk-space
  - storage
aliases:
  - Find Largest Folders PowerShell
  - Disk Usage Analyzer PowerShell
publish: true
permalink: powershell/identifying-whats-filling-up-a-disk
---

# <span class="rune">ᚲ</span> Identifying What's Filling Up a Disk

> *Knowing a drive is 95% full is the easy half of the question. The half that actually leads to a fix is knowing which specific folder is responsible — and that means walking the tree, not just reading the gauge.*

---

## 🎯 Problem

A drive reported as nearly full in [[Finding Free Disk Space Across All Drives]] needs a follow-up: which specific folders or files are actually responsible for the space, so something can actually be cleaned up or moved.

More generally, this pattern answers:
> Which folders (and files) on this drive are consuming the most space, so cleanup effort goes to the right place?

---

## 🤔 Mental Model

Think of this like tracing a water bill spike back to a specific leaking fixture — knowing the total bill went up tells you nothing about *where* to look; you have to check room by room, then fixture by fixture within the worst room, until the actual source is found. Recursively summing folder sizes and sorting descending does exactly that: narrow from "the whole drive" to "this one folder" to, eventually, "these specific files."

---

## 🧠 Why This Pattern Works

`Get-ChildItem -Recurse` combined with `Measure-Object -Property Length -Sum` computes the total size of everything under a given folder — there's no built-in "folder size" property the way there is for a file, so this pattern is the standard way to derive one. Running this at the top level first, then drilling into whichever subfolder turns out to be the largest, avoids the far slower approach of recursively measuring every single subfolder on a huge drive all at once.

---

## 💻 PowerShell Solution

```powershell
# Top-level folder sizes under a drive — start broad, then drill down
Get-ChildItem -Path 'C:\' -Directory | ForEach-Object {
    $size = (Get-ChildItem -Path $_.FullName -Recurse -File -ErrorAction SilentlyContinue |
        Measure-Object -Property Length -Sum).Sum
    [pscustomobject]@{
        Folder = $_.FullName
        SizeGB = [math]::Round($size / 1GB, 2)
    }
} | Sort-Object SizeGB -Descending

# Once the worst folder is identified, find its largest individual files
Get-ChildItem -Path 'C:\Logs' -Recurse -File -ErrorAction SilentlyContinue |
    Sort-Object Length -Descending |
    Select-Object -First 10 FullName, @{N='SizeMB';E={[math]::Round($_.Length/1MB,1)}}
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Measure size one level at a time, starting broad

```powershell
Get-ChildItem -Path 'C:\' -Directory | ForEach-Object { ... Measure-Object -Property Length -Sum ... }
```

Measuring every top-level folder's total recursive size first (rather than trying to enumerate the entire drive's files at once) keeps the initial pass reasonably fast and immediately points toward which specific area deserves deeper investigation.

### Step 2 — Sort to find the worst offender

```powershell
Sort-Object SizeGB -Descending
```

Surfaces the largest folder at the top — the natural next place to drill into.

### Step 3 — Drill into the worst folder for specific large files

```powershell
Get-ChildItem -Path 'C:\Logs' -Recurse -File | Sort-Object Length -Descending | Select-Object -First 10
```

Once narrowed to a specific folder, listing its individual largest files is what actually identifies a concrete cleanup target — a runaway log file, an old backup archive, or an abandoned large download.

---

## 🚀 Common Use Cases

- The direct follow-up investigation after [[Finding Free Disk Space Across All Drives]] flags a drive running low
- Identifying a runaway application log file that's grown unexpectedly large over time
- Finding old backup, installer, or archive files safe to clean up or relocate
- Building a scheduled report of the largest folders on a server to catch growth trends before they become urgent

---

## ⚖️ Alternatives

### Using WinDirStat/TreeSize-style third-party tools for interactive visual exploration

For a one-off interactive investigation with a GUI available, a dedicated disk usage visualization tool can be faster to explore than a scripted approach — the PowerShell pattern here remains the better choice specifically when remote, unattended, or scriptable investigation is needed.

### Finding files older than a certain age as cleanup candidates

```powershell
Get-ChildItem -Path 'C:\Logs' -Recurse -File |
    Where-Object LastWriteTime -lt (Get-Date).AddDays(-90) |
    Measure-Object -Property Length -Sum
```

Useful when the cleanup criterion is age rather than pure size — old, untouched files are often safer to remove than simply the largest ones, which might still be actively in use.

### Remote check across a fleet

```powershell
Invoke-Command -ComputerName 'srv01' -ScriptBlock {
    Get-ChildItem -Path 'D:\' -Directory | ForEach-Object {
        [pscustomobject]@{
            Folder = $_.FullName
            SizeGB = [math]::Round(((Get-ChildItem $_.FullName -Recurse -File -ErrorAction SilentlyContinue | Measure-Object Length -Sum).Sum) / 1GB, 2)
        }
    } | Sort-Object SizeGB -Descending | Select-Object -First 5
}
```

---

## ⚠️ Common Mistakes

- Running a single unbounded `Get-ChildItem -Recurse` against an entire large drive at once instead of measuring top-level folders first — dramatically slower and produces a huge, hard-to-navigate result set all at once.
- Not suppressing access-denied errors (`-ErrorAction SilentlyContinue`) when recursing through system folders, causing a script to halt or produce noisy errors on folders the current user can't fully access.
- Deleting a large file found this way without confirming it isn't actively in use or required (an active log file still being written to, for instance) — check with [[Finding Which Process Is Locking a File]] first if there's any doubt.
- Measuring size by file **count** instead of total **bytes** — a folder with a million tiny files and a folder with ten enormous ones can look similar or backwards depending on which metric is checked.

---

## 💡 Wisdom from Mímir

Always measure top-level folders first and drill down from the worst offender — resist the temptation to recurse the entire drive in one pass. The narrowing approach finds the same answer, just dramatically faster, and gives useful intermediate results even before the full investigation finishes.

---

## 🔗 Related Notes

- [[Finding Free Disk Space Across All Drives]]
- [[Checking Disk Health and SMART Status]]
- [[Finding Which Process Is Locking a File]]
- [[Storage Cmdlets]]
