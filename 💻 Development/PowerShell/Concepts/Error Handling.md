---
type: concept
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Intermediate
tags:
  - powershell-fundamentals
  - error-handling
  - try-catch
aliases:
  - Try Catch PowerShell
  - PowerShell Error Handling
publish: true
permalink: powershell/error-handling
---

# <span class="rune">ᛟ</span> Error Handling

> *A script that only handles the errors it expects isn't handling errors — it's hoping. `try/catch` only catches what's actually terminating; the rest slips by unless you make it stop first.*

---

## 🎯 Purpose

PowerShell distinguishes between **terminating** errors (which stop execution of the current command and can be caught) and **non-terminating** errors (which get written to the error stream and execution continues). This distinction trips up nearly everyone coming from languages where all exceptions behave the same way — a `try/catch` block that "isn't catching the error" is almost always failing to catch a non-terminating one.

Reliable automation depends on understanding this split, because a script that silently continues past a failed step (deleting only half a set of files, connecting to only some of ten servers) can cause worse damage than one that stops cleanly and reports what went wrong.

---

## 🧠 Key Ideas

- **Terminating errors** stop the current command/script and can be caught by `try/catch` — things like a thrown exception or an unhandled type conversion failure.
- **Non-terminating errors** (the default for most cmdlet failures, like `Get-Item` on a missing path) are written to the error stream and execution continues to the next line — `try/catch` will NOT catch these unless told to.
- `-ErrorAction Stop` forces a specific cmdlet call to treat its own errors as terminating, making it catchable.
- `$ErrorActionPreference` sets the default behavior for the whole script/session (commonly set to `'Stop'` at the top of scripts that need reliable error handling throughout).
- `$Error` is an automatic variable holding a running list of every error encountered in the session, most recent first (`$Error[0]`).
- `finally` runs regardless of whether the `try` succeeded or the `catch` fired — used for cleanup that must always happen (closing a connection, releasing a lock).

---

## ⚙️ How It Works

By default, most cmdlets treat their own failures as non-terminating: they write a red error message to the error stream and move on to the next line, because stopping an entire script over one missing file (for example) is often not what's wanted. This means a `try { Get-Item 'C:\missing.txt' } catch { 'caught it' }` block will **not** print "caught it" — the error was written to the stream, but the `try` block didn't actually terminate, so there was nothing for `catch` to intercept.

Adding `-ErrorAction Stop` to that same call converts it into a terminating error for that one command, which `try/catch` will now correctly catch. Setting `$ErrorActionPreference = 'Stop'` at the top of a script applies that same conversion to every cmdlet call for the rest of the script, without needing to add `-ErrorAction Stop` to each one individually.

```text
Get-Item 'C:\missing.txt'                       → non-terminating, try/catch does NOT catch it
Get-Item 'C:\missing.txt' -ErrorAction Stop      → terminating, try/catch DOES catch it
$ErrorActionPreference = 'Stop'; Get-Item ...    → terminating by default for the rest of the script
```

---

## 💻 Examples

```powershell
# This does NOT get caught — Get-Item errors are non-terminating by default
try {
    Get-Item 'C:\DoesNotExist.txt'
} catch {
    Write-Output "This never runs"
}

# This DOES get caught
try {
    Get-Item 'C:\DoesNotExist.txt' -ErrorAction Stop
} catch {
    Write-Output "Caught it: $($_.Exception.Message)"
}

# finally always runs, for cleanup
try {
    $conn = Connect-Something
    Do-Work -Connection $conn
} catch {
    Write-Warning "Failed: $($_.Exception.Message)"
} finally {
    if ($conn) { Disconnect-Something -Connection $conn }
}

# Setting script-wide behavior once
$ErrorActionPreference = 'Stop'
Get-Item 'C:\DoesNotExist.txt'   # now terminates, caught by any surrounding try/catch
```

---

## 🚀 Real World Applications

- Wrapping remote connection attempts (`Invoke-Command`, `New-PSSession`) in `try/catch` to report which specific server failed instead of aborting a whole bulk operation
- Using `finally` to guarantee a mapped drive, opened file handle, or remote session is cleaned up even if the middle of a script throws
- Setting `$ErrorActionPreference = 'Stop'` at the top of provisioning/deprovisioning scripts where a silent partial failure would be worse than a hard stop
- Inspecting `$_.Exception.Message` and `$_.CategoryInfo` inside a `catch` block to log a specific, actionable reason for a failure

---

## ⚖️ Advantages

- The terminating/non-terminating split lets a script choose, per command, whether a failure should be recoverable or fatal.
- `try/catch/finally` syntax is directly familiar to anyone coming from C#, Java, JavaScript, or Python.
- `$Error` provides a full session history of failures without needing to have wrapped every command in its own try/catch in advance.
- Structured error objects (`$_.Exception`, `$_.CategoryInfo`, `$_.TargetObject`) give far more diagnostic detail than a plain error string.

---

## ⚠️ Limitations

- The non-terminating default is invisible until you specifically go looking for it — a script can appear to "handle errors" in testing while actually catching nothing, if none of the errors during testing happened to be terminating.
- Overusing `$ErrorActionPreference = 'Stop'` globally can make otherwise-recoverable minor errors (like one missing optional file) fatal to an entire script.
- `-ErrorAction SilentlyContinue` suppresses error *output* but not necessarily the underlying failure state — code after it must still check whether the operation actually succeeded.
- Not all errors are simple exceptions; some carry rich `ErrorRecord` data that's easy to under-use by only reading `.Exception.Message`.

---

## 🚨 Common Mistakes

- Wrapping a cmdlet in `try/catch` without `-ErrorAction Stop` and assuming the catch block will fire — it silently won't for non-terminating errors.
- Using `-ErrorAction SilentlyContinue` to "make an error go away" without checking afterward whether the operation actually succeeded, hiding real failures.
- Catching every error the same generic way instead of inspecting `$_.Exception.GetType().Name` or `$_.FullyQualifiedErrorId` to handle different failure types differently.
- Forgetting that `$_` inside a `catch` block refers to the error record, not the pipeline object — reusing `$_` right after a `ForEach-Object` block can cause confusion about which `$_` is in scope.

---

## 📖 Further Reading

- `Get-Help about_Try_Catch_Finally`
- `Get-Help about_Preference_Variables` (for `$ErrorActionPreference`)
- `Get-Help about_Automatic_Variables` (for `$Error` and `$_`)

---

## 💡 Wisdom from Mímir

If a `try/catch` block "isn't working," the first thing to check is whether `-ErrorAction Stop` is missing — not whether the catch logic itself is wrong. Nine times out of ten, the error was never terminating in the first place.

---

## 🔗 Related Notes

- [[The Pipeline]]
- [[Functions]]
- [[Loops and Iteration]]
- [[PowerShell Codex]]
