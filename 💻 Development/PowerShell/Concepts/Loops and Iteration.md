---
type: concept
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Beginner
tags:
  - powershell-fundamentals
  - loops
  - iteration
aliases:
  - ForEach-Object
  - PowerShell Loops
publish: true
permalink: powershell/loops-and-iteration
---

# <span class="rune">ᛟ</span> Loops and Iteration

> *PowerShell gives you two ways to repeat an action — a statement that loops over a variable already in memory, and a pipeline stage that reacts to objects as they arrive. Picking the wrong one is rarely wrong, just slower.*

---

## 🎯 Purpose

Iteration is how a script repeats an action across a set of items — every computer name in a list, every file in a folder, every log entry over a date range. PowerShell provides both classic statement-based loops (`foreach`, `for`, `while`, `do-while`) and a pipeline cmdlet, `ForEach-Object`, that iterates over whatever's flowing through [[The Pipeline|the pipeline]].

Knowing when to reach for which isn't just style — it affects memory usage, performance, and how naturally the code reads for the task at hand.

---

## 🧠 Key Ideas

- `foreach ($item in $collection) { }` is a **statement** — it requires the full collection to already exist in memory (e.g., in a variable).
- `ForEach-Object { }` is a **pipeline cmdlet** — it processes each object as it streams through, one at a time, without needing the whole collection loaded first.
- `for ($i=0; $i -lt 10; $i++) { }` is used when you need explicit control over an index or counter, not just "for every item."
- `while` and `do-while`/`do-until` loop based on a condition rather than a fixed collection — useful for polling or retry logic.
- Inside `ForEach-Object`, the current object is `$_`/`$PSItem` — exactly like inside `Where-Object`.
- `break` exits a loop entirely; `continue` skips to the next iteration — both work in statement loops, and `continue`/`break` also work inside `ForEach-Object` script blocks in modern PowerShell versions.

---

## ⚙️ How It Works

The `foreach` statement and `ForEach-Object` cmdlet look similar but work fundamentally differently. `foreach` needs its collection fully realized in memory before the loop starts — fine for a few thousand items, wasteful for millions. `ForEach-Object`, being a real pipeline stage, receives and processes one object at a time as it's produced upstream, so it can start working before the source has even finished generating everything — the same streaming behavior [[The Pipeline]] describes generally.

`for` and `while` are lower-level control structures independent of collections entirely — they loop purely based on a condition, making them the right tool for retry logic ("keep trying until this succeeds or 5 attempts pass") rather than "for every item in this set."

```text
$list = Get-Process              # collection fully materialized first
foreach ($p in $list) {...}      # then looped over in memory

Get-Process | ForEach-Object {...}   # streamed — no full collection needed
```

---

## 💻 Examples

```powershell
# foreach statement — collection already in a variable
$servers = 'srv01','srv02','srv03'
foreach ($server in $servers) {
    Test-Connection -ComputerName $server -Count 1 -Quiet
}

# ForEach-Object — streaming through the pipeline
Get-Content .\servers.txt | ForEach-Object {
    Test-Connection -ComputerName $_ -Count 1 -Quiet
}

# for loop — explicit counter
for ($i = 1; $i -le 5; $i++) {
    Write-Output "Attempt $i"
}

# while loop — condition-driven retry
$attempts = 0
do {
    $attempts++
    $result = Test-Connection -ComputerName 'srv01' -Count 1 -Quiet
} while (-not $result -and $attempts -lt 5)
```

---

## 🚀 Real World Applications

- Looping over a CSV of usernames to bulk-create or bulk-modify AD accounts
- Streaming `Get-ChildItem -Recurse` through `ForEach-Object` to process huge directory trees without loading every file into memory first
- Polling a service or endpoint with a `while` loop until it comes back online, with a max-attempt cutoff
- Iterating a fixed list of remote computers to run the same diagnostic command against each

---

## ⚖️ Advantages

- `ForEach-Object`'s streaming behavior keeps memory flat even against enormous input sets.
- `foreach` statements are noticeably faster than `ForEach-Object` for collections already fully in memory, since there's no pipeline overhead per item.
- `for`/`while` give precise control for counter-based or condition-based logic that doesn't map cleanly to "for every item."
- All four forms share the same `break`/`continue` vocabulary, so switching between them doesn't require relearning control flow.

---

## ⚠️ Limitations

- `ForEach-Object` has real per-object pipeline overhead — noticeable in tight loops over very large in-memory collections where `foreach` would be faster.
- `foreach` statements require the entire collection up front, which can be a real memory problem against huge datasets (e.g., millions of log lines).
- `do-while`/`do-until` always run the body **at least once**, even if the condition is false from the start — easy to forget and cause an unwanted first execution.
- Nesting multiple loop types together without clear naming can make `break`/`continue` targets ambiguous — labeled loops (`:outer`) exist for this but are rarely used and easy to forget.

---

## 🚨 Common Mistakes

- Using `ForEach-Object` reflexively out of pipeline habit, even when the data is already a variable — a plain `foreach` statement is usually simpler and faster there.
- Forgetting `while` conditions must already be true to run the body *at all* — if the initial state should always execute once regardless, `do-while` is the correct choice instead.
- Writing an unbounded `while ($true) {}` retry loop with no attempt counter or timeout, causing a script to hang indefinitely against a service that never comes back.
- Modifying the collection being iterated by a `foreach` statement mid-loop (e.g., removing items from the same array) — this produces unpredictable skipped or repeated elements.

---

## 📖 Further Reading

- `Get-Help about_Foreach`
- `Get-Help ForEach-Object -Full`
- `Get-Help about_While`
- `Get-Help about_Break` / `Get-Help about_Continue`

---

## 💡 Wisdom from Mímir

Default to `ForEach-Object` when the data is arriving from a pipeline (files, AD queries, event logs), and default to `foreach` when the data is already sitting in a variable. That single rule of thumb resolves most "which loop should I use" hesitation without needing to benchmark anything.

---

## 🔗 Related Notes

- [[The Pipeline]]
- [[Objects in PowerShell]]
- [[Error Handling]]
- [[PowerShell Codex]]
