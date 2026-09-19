---
type: powershell-pattern
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Intermediate
tags:
  - authentication
  - credentials
  - account-lockout
aliases:
  - Validate AD Credentials
  - DirectoryServices AccountManagement
publish: true
permalink: powershell/testing-credentials-without-locking-an-account
---

# <span class="rune">ᚲ</span> Testing Credentials Without Locking an Account

> *Every failed guess counts against the lockout threshold — a troubleshooting method that "tests" a password by repeatedly trying to log in is the same mechanism that causes the lockout it's supposedly diagnosing.*

---

## 🎯 Problem

A user insists they're typing their password correctly, but authentication keeps failing. Confirm whether a specific username/password combination is actually valid — without risking triggering a lockout through repeated failed attempts.

More generally, this pattern answers:
> How do I validate a username and password against Active Directory in a single, controlled check, distinct from an actual interactive logon attempt?

---

## 🤔 Mental Model

Think of credential validation like a bank teller checking a signature against one on file — a single, deliberate comparison — versus a user standing at an ATM repeatedly guessing a PIN. `System.DirectoryServices.AccountManagement`'s `ValidateCredentials` method performs exactly one authentication check against the domain, the same as one real logon attempt would, but it's a single, controlled, scriptable comparison — not a loop of blind guesses, and not fundamentally different from what one careful manual login attempt would already do.

---

## 🧠 Why This Pattern Works

The `System.DirectoryServices.AccountManagement` .NET namespace exposes a `PrincipalContext` object representing a connection to a domain, and its `ValidateCredentials(username, password)` method performs a single bind attempt against a domain controller, returning a simple `$true`/`$false`. This is functionally equivalent to one real logon attempt — it counts toward the lockout threshold exactly like any other failed authentication would — so the actual safety comes from calling it **once** per check, deliberately, rather than embedding it in any kind of retry loop.

---

## 💻 PowerShell Solution

```powershell
Add-Type -AssemblyName System.DirectoryServices.AccountManagement

$domain = 'contoso.com'
$username = 'jdoe'
$password = Read-Host -Prompt 'Enter password to test' -AsSecureString
$plainPassword = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto(
    [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($password)
)

$context = New-Object System.DirectoryServices.AccountManagement.PrincipalContext('Domain', $domain)
$isValid = $context.ValidateCredentials($username, $plainPassword)

if ($isValid) {
    Write-Output "Credentials are VALID for $username"
} else {
    Write-Output "Credentials are INVALID for $username"
}
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Load the required .NET assembly

```powershell
Add-Type -AssemblyName System.DirectoryServices.AccountManagement
```

This namespace isn't loaded into a PowerShell session by default and must be added explicitly before its types are usable.

### Step 2 — Establish a domain context

```powershell
$context = New-Object System.DirectoryServices.AccountManagement.PrincipalContext('Domain', $domain)
```

Represents the connection target for the validation check — the domain being authenticated against.

### Step 3 — Perform exactly one validation check

```powershell
$context.ValidateCredentials($username, $plainPassword)
```

Returns a straightforward boolean — this single call is the entire mechanism, deliberately not wrapped in any retry logic that could risk multiple failed attempts.

---

## 🚀 Common Use Cases

- Confirming a user is genuinely typing the correct password before assuming a deeper account or system issue
- Validating a service account's credentials after a scheduled password rotation, before deploying it into a scheduled task or application config
- Pre-flight credential checks in a provisioning script, to fail fast with a clear message rather than deep inside a later step
- Diagnosing "wrong password" complaints where autocomplete, caps lock, or keyboard layout is suspected as the real cause

---

## ⚖️ Alternatives

### Checking BadPasswordCount before AND after, to confirm exactly one attempt occurred

```powershell
$before = (Get-ADUser -Identity 'jdoe' -Properties badPwdCount).badPwdCount
# ... run ValidateCredentials once ...
$after = (Get-ADUser -Identity 'jdoe' -Properties badPwdCount).badPwdCount
Write-Output "Bad password count went from $before to $after"
```

A useful sanity check the first few times this pattern is used, confirming the validation call behaves as expected without accidentally looping.

### Testing against a local machine account instead of a domain

```powershell
$context = New-Object System.DirectoryServices.AccountManagement.PrincipalContext('Machine', $env:COMPUTERNAME)
$context.ValidateCredentials('localadmin', $plainPassword)
```

Same mechanism, targeted at the local SAM database instead of AD — useful for validating a local account's password on a specific machine.

---

## ⚠️ Common Mistakes

- Wrapping `ValidateCredentials` in a loop trying multiple password guesses — this is functionally a brute-force attempt against the account and will trigger the exact lockout this pattern exists to avoid.
- Leaving the plain-text password sitting in `$plainPassword` in memory or script history longer than necessary — it should be used immediately and the variable cleared/scoped tightly afterward.
- Forgetting `Add-Type -AssemblyName System.DirectoryServices.AccountManagement` and getting a confusing "type not found" error instead of a clear missing-assembly message.
- Assuming a `$false` result always means "wrong password" — it can also mean the account is disabled, locked out, or expired; `ValidateCredentials` doesn't distinguish the reason, only the outcome.

---

## 💡 Wisdom from Mímir

`ValidateCredentials` should be called exactly once per check, on purpose, every time — the instant it's used inside a loop or a retry mechanism, it stops being a diagnostic tool and becomes the very brute-force pattern that causes lockouts in the first place.

---

## 🔗 Related Notes

- [[PowerShell Security Fundamentals]]
- [[Finding and Unlocking a Locked-Out AD Account]]
- [[Checking a User's Current Logon Session]]
- [[Active Directory Cmdlets]]
