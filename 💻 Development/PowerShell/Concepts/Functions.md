---
type: concept
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Intermediate
tags:
  - powershell-fundamentals
  - functions
  - cmdletbinding
aliases:
  - PowerShell Functions
  - Advanced Functions
publish: true
permalink: powershell/functions
---

# <span class="rune">ᛟ</span> Functions

> *A script that works once is a script. A script wrapped in a well-formed function, with real parameters and validation, is a tool — one you can hand to someone else, or your future self, without a walkthrough.*

---

## 🎯 Purpose

Functions package reusable logic behind a name, exactly like in any programming language — but PowerShell functions can also become **advanced functions** that behave identically to built-in cmdlets: named parameters, pipeline input, validation attributes, `-WhatIf`/`-Confirm` support, and proper help text. The gap between "script I wrote" and "cmdlet I could publish in a module" is smaller in PowerShell than almost any other language.

This matters directly for troubleshooting and automation work: turning a one-off diagnostic one-liner into a properly parameterized function is what makes it reusable across different servers, users, or dates without editing the code each time.

---

## 🧠 Key Ideas

- Basic syntax: `function Verb-Noun { param($Name) ... }` — PowerShell strongly encourages the `Verb-Noun` naming convention (`Get-`, `Set-`, `Test-`, `Invoke-`) used by every built-in cmdlet.
- `[CmdletBinding()]` above the `param()` block turns a plain function into an **advanced function**, unlocking common parameters (`-Verbose`, `-ErrorAction`, `-WhatIf` when combined with `SupportsShouldProcess`) automatically.
- Parameter attributes add validation and behavior without extra code: `[Parameter(Mandatory)]`, `[ValidateSet('A','B')]`, `[ValidateRange(1,100)]`, `[ValidateNotNullOrEmpty()]`.
- Functions can accept pipeline input directly via `[Parameter(ValueFromPipeline)]` or `[Parameter(ValueFromPipelineByPropertyName)]`, making a custom function behave exactly like a native cmdlet in a pipeline.
- `return` exits a function early with a value, but **any unsuppressed output inside a function is also returned** — a very common source of bugs when debug `Write-Output` calls accidentally become part of the function's actual return value.

---

## ⚙️ How It Works

`[CmdletBinding()]` is what separates a plain function from an advanced one. Without it, `param()` just declares simple positional/named arguments. With it, PowerShell's own parameter-binding engine takes over — the same engine that powers every built-in cmdlet — adding automatic support for common parameters, strict parameter validation, and pipeline binding behavior identical to native commands.

Because PowerShell functions don't have an explicit "return type," **everything written to the output stream inside the function becomes part of what it returns** — including a stray `Write-Output "debugging..."` left in during testing. This is different from languages where only an explicit `return` statement produces a value, and it's the single most common cause of "my function returns the wrong thing" bugs.

```powershell
function Get-Example {
    [CmdletBinding()]
    param(
        [Parameter(Mandatory, ValueFromPipeline)]
        [string]$ComputerName
    )
    process {
        "checking $ComputerName"   # ⚠ this line is ALSO returned — not just a log message
        Test-Connection -ComputerName $ComputerName -Count 1 -Quiet
    }
}
```

---

## 💻 Examples

```powershell
# Simple function
function Get-Greeting {
    param([string]$Name)
    "Hello, $Name"
}

# Advanced function with validation and pipeline support
function Test-ServerOnline {
    [CmdletBinding()]
    param(
        [Parameter(Mandatory, ValueFromPipeline, ValueFromPipelineByPropertyName)]
        [ValidateNotNullOrEmpty()]
        [string]$ComputerName
    )
    process {
        [pscustomobject]@{
            ComputerName = $ComputerName
            Online       = Test-Connection -ComputerName $ComputerName -Count 1 -Quiet
        }
    }
}

# Now works exactly like a built-in cmdlet, including pipeline input
'srv01','srv02','srv03' | Test-ServerOnline

# WhatIf support for a state-changing function
function Remove-TempFiles {
    [CmdletBinding(SupportsShouldProcess)]
    param([string]$Path)
    if ($PSCmdlet.ShouldProcess($Path, 'Remove temp files')) {
        Remove-Item $Path -Recurse -Force
    }
}
Remove-TempFiles -Path 'C:\Temp' -WhatIf
```

---

## 🚀 Real World Applications

- Turning a repeated troubleshooting one-liner (checking a service, pinging a host, reading a log) into a reusable function shared across a team via a module
- Building bulk-operation tools (bulk password reset, bulk software check) that accept computer or user names from the pipeline
- Adding `-WhatIf` support to any function that deletes, modifies, or resets something, so it can be safely dry-run before actually executing
- Using `[ValidateSet()]` on a parameter to constrain valid input (e.g., an `-Environment` parameter limited to `'Dev','Test','Prod'`) and catch typos before the function body even runs

---

## ⚖️ Advantages

- Advanced functions are indistinguishable from real cmdlets to the person calling them — same parameter discovery via tab-completion, same `Get-Help` support.
- Parameter validation attributes catch bad input before any logic runs, instead of failing deep inside the function body with a confusing error.
- `SupportsShouldProcess` gives any state-changing function `-WhatIf`/`-Confirm` for free, a huge safety win for destructive operations.
- Pipeline-aware parameters let custom functions compose naturally with the rest of PowerShell's built-in cmdlets.

---

## ⚠️ Limitations

- The "everything unsuppressed is returned" behavior requires discipline — `Write-Output`/bare expressions used for debugging must be removed or redirected to `Write-Verbose`/`Write-Host` before the function ships.
- `[CmdletBinding()]` adds a small amount of overhead compared to a plain function — negligible for almost all real scripts, but noticeable in extremely tight loops.
- Advanced function parameter validation happens at bind time, which can make debugging "why didn't my function even start" slightly less obvious than a runtime check inside the body.
- PowerShell has no true function overloading — a function name maps to one parameter set (or one of several explicitly defined parameter sets), not multiple independent signatures.

---

## 🚨 Common Mistakes

- Leaving a stray unsuppressed line (a debug `"here"` or `$variable` on its own line) inside a function, which silently becomes part of the function's return value alongside the intended output.
- Forgetting `[CmdletBinding(SupportsShouldProcess)]` requires actually calling `$PSCmdlet.ShouldProcess()` inside the function body — adding the attribute alone does nothing by itself.
- Using `return $value` out of habit from other languages when a bare `$value` on its own line would work identically — not wrong, but often a sign the "everything is output" model hasn't fully clicked yet.
- Not using `[Parameter(Mandatory)]` for genuinely required parameters, leading to confusing `$null`-related errors deep inside the function body instead of a clear prompt or error up front.

---

## 📖 Further Reading

- `Get-Help about_Functions_Advanced`
- `Get-Help about_Functions_CmdletBindingAttribute`
- `Get-Help about_Functions_Advanced_Parameters`
- `Get-Help about_Functions_OutputTypeAttribute`

---

## 💡 Wisdom from Mímir

Before calling a function "done," scan its body for any line that isn't explicitly assigned to a variable, passed to `Write-Verbose`, or intended as the actual return value. In PowerShell, an orphaned expression isn't a no-op — it's silently part of the output.

---

## 🔗 Related Notes

- [[Error Handling]]
- [[Objects in PowerShell]]
- [[Modules]]
- [[PowerShell Codex]]
