---
type: concept
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Beginner
tags:
  - powershell-fundamentals
  - pipeline
  - mental-model
aliases:
  - PowerShell Pipeline
  - The PowerShell Pipeline
publish: true
permalink: powershell/the-pipeline
---

# <span class="rune">ᛟ</span> The Pipeline

> *A pipeline built from objects doesn't just connect commands — it lets each command specialize in doing exactly one thing well, and trust the next one to do the rest.*

---

## 🎯 Purpose

The `|` pipe operator chains commands together so the output of one becomes the input of the next — a concept borrowed from Unix shells, but supercharged by [[Objects in PowerShell|PowerShell's object model]]. Because whole objects flow through the pipe instead of text, each stage can filter, transform, or act on real properties without re-parsing anything.

This is the concept that turns a list of individual cmdlets into an actual toolkit. Nearly every real PowerShell one-liner is a pipeline of three or four small, single-purpose commands strung together.

---

## 🧠 Key Ideas

- Data flows left to right: `Get-Process | Where-Object {...} | Sort-Object CPU | Select-Object -First 5`.
- Each stage receives the **objects** the previous stage output — not text — and can act on their real properties immediately.
- Objects can bind to the next cmdlet's parameters two ways: **ByValue** (the whole object matches a parameter's expected type) or **ByPropertyName** (a property on the object matches a parameter name exactly).
- Inside a pipeline stage's script block, `$_` (or its modern alias `$PSItem`) represents "the current object flowing through" at that point.
- The pipeline processes objects **one at a time**, streaming — it doesn't wait to collect everything before passing it on, which is why pipelines can handle huge datasets without loading everything into memory at once.

---

## ⚙️ How It Works

Every cmdlet supports up to three processing blocks under the hood: `Begin` (runs once, before any pipeline input arrives), `Process` (runs once *per object* that comes through the pipe), and `End` (runs once, after all input is exhausted). This is why a pipeline can start producing output before the first cmdlet has even finished — each object is handed downstream as soon as it's ready, rather than being batched.

Parameter binding decides how an object connects to the next cmdlet. PowerShell first tries ByValue (does the whole incoming object's type match what a parameter accepts?); if that fails, it tries ByPropertyName (does the object have a property whose name exactly matches a parameter name?). Get-Help `-full` on any cmdlet shows which parameters accept pipeline input and how.

```text
Get-Service | Where-Object {$_.Status -eq 'Running'} | Stop-Service
      |                    |                                |
  emits Service        $_ = each Service              binds by property
   objects              object in turn                 (-Name accepts
                                                          ByPropertyName)
```

---

## 💻 Examples

```powershell
# Classic three-stage pipeline: get, filter, act
Get-Process | Where-Object {$_.CPU -gt 100} | Stop-Process -WhatIf

# ByPropertyName binding: Name from Get-Process objects binds to -Name
Get-Process notepad | Stop-Process

# $PSItem is identical to $_ (newer, more readable alias)
1..10 | ForEach-Object { $PSItem * 2 }

# Streaming in action — output appears per object, not all at once
Get-ChildItem C:\Windows -Recurse | ForEach-Object { $_.Name }
```

---

## 🚀 Real World Applications

- Chaining `Get-ADUser -Filter * | Where-Object {...} | Export-Csv` for a filtered AD report in one line
- Piping `Get-EventLog`/`Get-WinEvent` into `Where-Object` and `Select-Object` to isolate specific error events
- Combining `Get-Process | Sort-Object | Select-Object -First N` for quick top-N reports without writing a script
- Feeding `Get-ChildItem` output directly into `Remove-Item` or `Copy-Item` for bulk file operations

---

## ⚖️ Advantages

- Complex multi-step logic collapses into a single readable line.
- Streaming behavior keeps memory usage low even against very large object collections.
- Because everything is typed, you rarely need intermediate variables just to reshape data between steps.
- The same pipeline pattern works identically across local files, AD, processes, the registry, and remote sessions.

---

## ⚠️ Limitations

- Deeply nested pipelines can become hard to read and debug — sometimes a variable-and-loop approach is clearer.
- Not every parameter accepts pipeline input; check `Get-Help <cmdlet> -Full` before assuming binding will "just work."
- ByPropertyName binding fails silently if property names don't match exactly, which can produce confusing partial results.
- Performance can suffer if a Begin/Process/End-unaware custom function is used inside a large pipeline instead of natively supporting streaming.

---

## 🚨 Common Mistakes

- Forgetting `$_`/`$PSItem` only has meaning *inside* a script block passed to something like `Where-Object` or `ForEach-Object`.
- Assuming ByValue binding will work for any object of a vaguely related type — it requires an exact type match.
- Writing `Get-Process | Format-Table | Export-Csv` — piping *formatted* output (from `Format-Table`) into something expecting real objects breaks it, because formatting output is for display only, not further processing.
- Using `ForEach-Object` when a plain `foreach` statement would be clearer and faster for in-memory collections already fully loaded into a variable.

---

## 📖 Further Reading

- `Get-Help about_Pipelines`
- `Get-Help about_Automatic_Variables` (for `$_` / `$PSItem`)
- Microsoft Learn: "About pipelines"

---

## 💡 Wisdom from Mímir

If a pipeline stage's output looks wrong, check whether the stage *before* it was `Format-Table`, `Format-List`, or `Out-String` — formatting cmdlets are pipeline dead ends. Nothing meaningful should come after them except more display.

---

## 🔗 Related Notes

- [[Objects in PowerShell]]
- [[Filtering and Comparison Operators]]
- [[Loops and Iteration]]
- [[PowerShell Codex]]
