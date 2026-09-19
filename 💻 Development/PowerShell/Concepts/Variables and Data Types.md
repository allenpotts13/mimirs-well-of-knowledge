---
type: concept
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Beginner
tags:
  - powershell-fundamentals
  - variables
  - data-types
aliases:
  - PowerShell Variables
  - PowerShell Data Types
publish: true
permalink: powershell/variables-and-data-types
---

# <span class="rune">ᛟ</span> Variables and Data Types

> *PowerShell will happily let a variable hold anything — the discipline of typing it anyway is what keeps a script honest as it grows.*

---

## 🎯 Purpose

Variables in PowerShell store values for reuse — command output, user input, configuration, loop state. Every variable name starts with `$`, and PowerShell is **dynamically but strongly typed**: a variable isn't declared with a fixed type up front, but once it holds a value, that value's real .NET type (`String`, `Int32`, `DateTime`, `Boolean`, array, hashtable, or a custom object) governs how it behaves.

Understanding this dual nature — flexible to assign, strict about what the underlying type actually allows — prevents a whole category of bugs where a script "works with these values" but breaks silently on slightly different input.

---

## 🧠 Key Ideas

- Declare and assign in one step: `$name = 'Allen'` — no separate type declaration required.
- Optional explicit typing constrains what a variable can hold: `[int]$count = 5` throws an error if later assigned something that can't convert to an integer.
- Common built-in types: `[string]`, `[int]`, `[double]`, `[bool]`, `[datetime]`, `[array]`, `[hashtable]`, `[pscustomobject]`.
- Arrays (`$list = 1,2,3` or `@(1,2,3)`) and hashtables (`$h = @{Name='Bob'; Age=30}`) are the two core collection types used everywhere in real scripts.
- `$null`, `$true`, and `$false` are built-in automatic variables — not strings — and behave like their real boolean/null equivalents in comparisons.
- Type conversion happens automatically in many contexts (string concatenation, arithmetic) but not always in the direction you'd expect — this is a frequent source of subtle bugs.

---

## ⚙️ How It Works

When you write `$count = 5`, PowerShell creates a variable whose underlying value is a real .NET `Int32` object — not a text label "5". Because PowerShell is built on .NET, every variable value is an object with its own type, methods, and properties, exactly like the objects flowing through [[Objects in PowerShell|the pipeline]] — a variable is just a named container pointing at one.

Explicit type constraints (`[int]$count`) are enforced at assignment time: PowerShell tries to convert whatever's assigned into that type, and throws a `MetadataError` if the conversion isn't possible. Without an explicit type, the variable simply becomes whatever type the assigned value already is, and can be reassigned to a completely different type later — this flexibility is convenient for quick scripts but can hide bugs in larger ones.

```text
$count = 5          → $count is [Int32], value 5
[int]$count = 5     → $count is CONSTRAINED to [Int32] going forward
$count = "hello"    → throws: cannot convert "hello" to Int32
```

---

## 💻 Examples

```powershell
# Basic assignment — type inferred automatically
$name = 'Allen'
$age = 34
$isAdmin = $true

# Explicit typing constrains future assignments
[int]$retryCount = 3

# Arrays
$servers = 'srv01', 'srv02', 'srv03'
$servers += 'srv04'          # arrays are actually resized/recreated under the hood

# Hashtables — key/value pairs, used constantly for parameters and lookups
$config = @{
    Server   = 'srv01'
    Port     = 443
    Protocol = 'HTTPS'
}
$config.Server

# Type conversion pitfalls
"5" + 3        # "53"  — string concatenation wins
5 + "3"        # 8     — numeric addition wins because the FIRST operand is numeric
```

---

## 🚀 Real World Applications

- Storing `Get-Credential` results in a variable to reuse across multiple remote commands
- Using hashtables as lightweight configuration objects (`splatting` parameters into cmdlets)
- Building arrays of computer names or usernames to loop through for bulk operations
- Explicit `[int]`/`[datetime]` typing on function parameters to fail fast on bad input instead of failing deep inside logic

---

## ⚖️ Advantages

- No boilerplate type declarations needed for quick, throwaway scripts.
- Explicit typing is available exactly when you need the safety net, without being mandatory everywhere.
- Full access to every .NET type and its methods, since PowerShell variables ARE .NET objects.
- Hashtables and arrays are first-class, lightweight, and used natively across the entire language (splatting, `ConvertTo-Json`, parameter blocks).

---

## ⚠️ Limitations

- Implicit type coercion (especially between strings and numbers) can produce results that surprise anyone not watching operand order closely.
- Arrays in PowerShell are fixed-size under the hood — `+=` on a large array in a loop silently recreates the whole array every iteration, which is a real performance trap.
- Without explicit typing, bugs from unexpected type changes (a variable that's sometimes a string, sometimes `$null`, sometimes an array of one) can surface far from where they were introduced.
- `[pscustomobject]` is convenient but doesn't enforce a fixed schema — two objects built from the same code path could end up with different properties if the code branches.

---

## 🚨 Common Mistakes

- Using `+=` on an array inside a large loop, not realizing every append rebuilds the entire array — use an `[System.Collections.Generic.List[object]]` or the pipeline itself instead for large collections.
- Comparing `$var -eq $null` instead of `$null -eq $var` — when `$var` is itself an array, the former can behave unexpectedly; put `$null` on the left as a defensive habit.
- Assuming a hashtable preserves insertion order — a plain `@{}` does not guarantee order; use `[ordered]@{}` when order matters.
- Confusing single-item arrays with scalars — `$x = @('only one')` is still an array, and code assuming `$x` is a plain string can break.

---

## 📖 Further Reading

- `Get-Help about_Variables`
- `Get-Help about_Arrays`
- `Get-Help about_Hash_Tables`
- `Get-Help about_Type_Conversion`

---

## 💡 Wisdom from Mímir

Put `$null` on the left side of an equality check as a reflex (`$null -eq $result`, never `$result -eq $null`). It costs nothing when `$result` is a scalar, and it saves you the day `$result` turns out to be an array.

---

## 🔗 Related Notes

- [[Objects in PowerShell]]
- [[Filtering and Comparison Operators]]
- [[Loops and Iteration]]
- [[PowerShell Codex]]
