---
type: concept
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Beginner
tags:
  - powershell-fundamentals
  - filtering
  - operators
aliases:
  - Where-Object
  - Comparison Operators
publish: true
permalink: powershell/filtering-and-comparison-operators
---

# <span class="rune">ᛟ</span> Filtering and Comparison Operators

> *Filtering isn't a separate skill from PowerShell — it's the same object model, asked a question instead of told to act.*

---

## 🎯 Purpose

`Where-Object` narrows a collection of objects down to just the ones that matter, using PowerShell's comparison operators to test each object's properties. This is the cmdlet equivalent of a SQL `WHERE` clause, and it's the single most-used stage in real-world pipelines — almost every troubleshooting one-liner is "get everything, then filter to the interesting subset."

Because PowerShell's comparison operators are words (`-eq`, `-gt`, `-like`) rather than symbols (`==`, `>`), and because `<` and `>` are reserved for redirection, this is one of the first syntax adjustments anyone coming from another language has to make.

---

## 🧠 Key Ideas

- Comparison operators are prefixed with a dash: `-eq`, `-ne`, `-gt`, `-ge`, `-lt`, `-le`.
- Wildcard and pattern operators extend this: `-like` (wildcard `*`/`?`), `-notlike`, `-match`/`-notmatch` (regular expressions), `-contains`/`-notcontains` (collection membership).
- `Where-Object` has two syntaxes: the classic script-block form (`Where-Object {$_.Status -eq 'Running'}`) and the newer simplified comparison-statement form (`Where-Object Status -eq 'Running'`) — both work identically for simple comparisons.
- Logical operators `-and`, `-or`, `-not`/`!` combine multiple conditions inside a script block.
- All comparison operators are **case-insensitive by default** — use the capitalized variants (`-ceq`, `-cmatch`, `-clike`, etc.) when case sensitivity matters.

---

## ⚙️ How It Works

`Where-Object` sits in the pipeline like any other cmdlet — it receives objects one at a time, evaluates the condition against each one using `$_`, and only passes objects through to the next stage when the condition evaluates to `$true`. Everything else is silently dropped.

`-eq` and friends compare the property on the *left* to the value on the *right*; when the left side is an array instead of a scalar, `-eq` switches behavior and returns every matching *element* from that array rather than a single true/false — a frequent source of confusion for anyone assuming it behaves like a boolean test in every case.

```text
Get-Service | Where-Object {$_.Status -eq 'Running'}
              |                |________________|
       filter stage        boolean test run
                            once per object
```

---

## 💻 Examples

```powershell
# Classic script-block syntax
Get-Process | Where-Object {$_.CPU -gt 50}

# Simplified syntax (no $_ needed for a single comparison)
Get-Process | Where-Object CPU -gt 50

# Wildcard matching
Get-Service | Where-Object {$_.Name -like 'win*'}

# Regular expression matching
Get-ChildItem | Where-Object {$_.Name -match '\.log$'}

# Combining conditions
Get-Process | Where-Object {$_.CPU -gt 50 -and $_.WorkingSet -gt 100MB}

# Collection membership
$approved = 'chrome','notepad','explorer'
Get-Process | Where-Object {$approved -contains $_.Name}
```

---

## 🚀 Real World Applications

- Filtering `Get-ADUser -Filter *` results down to accounts that haven't logged in for 90+ days
- Isolating specific event IDs or error levels from `Get-WinEvent`/`Get-EventLog` output
- Narrowing `Get-Process` to runaway CPU/memory consumers during a performance investigation
- Matching filenames or paths with `-match`/`-like` when bulk-processing files with `Get-ChildItem`

---

## ⚖️ Advantages

- Reads close to plain English once the dash-prefixed operators become familiar.
- Works identically on any object type — the same operators filter processes, services, files, or AD users.
- The simplified syntax removes boilerplate for the (very common) single-condition case.
- `-match` brings full .NET regular expression power directly into filtering.

---

## ⚠️ Limitations

- The simplified `Where-Object Property -op Value` syntax only supports **one** condition at a time — anything with `-and`/`-or` needs the script-block form.
- `-contains` checks whether a *collection* contains a value — it's easy to confuse with `-in`, which flips the direction (`$value -in $collection`).
- Case-insensitivity by default can hide bugs in scripts that assume exact case matching; must explicitly opt into `-ceq`/`-cmatch` when it matters.
- `-eq` against an array returns matching elements, not a boolean — a common source of "why did my if statement do that" bugs.

---

## 🚨 Common Mistakes

- Using `>` or `<` expecting numeric comparison — these are redirection operators in PowerShell; the correct operators are `-gt`/`-lt`.
- Mixing up `-contains` (collection contains value) and `-like` (wildcard string match) — they solve different problems and aren't interchangeable.
- Forgetting `-match` populates the automatic `$matches` variable with capture groups, then not using it when it would simplify extraction.
- Writing `Where-Object {$_.Name -eq $Name}` when `$Name` itself is `$null` — this compares against null and quietly returns nothing rather than erroring, hiding an upstream bug.

---

## 📖 Further Reading

- `Get-Help about_Comparison_Operators`
- `Get-Help Where-Object -Full`
- `Get-Help about_Wildcards`

---

## 💡 Wisdom from Mímir

When a filter "isn't working," check case sensitivity and `$null` values before suspecting the operator itself — PowerShell's forgiving defaults (case-insensitive, silent null comparisons) are convenient right up until they quietly hide the real bug.

---

## 🔗 Related Notes

- [[The Pipeline]]
- [[Objects in PowerShell]]
- [[Variables and Data Types]]
- [[PowerShell Codex]]
