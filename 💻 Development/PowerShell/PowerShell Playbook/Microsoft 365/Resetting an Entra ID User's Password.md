---
type: powershell-pattern
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Intermediate
tags:
  - microsoft-365
  - entra-id
  - password-reset
aliases:
  - Update-MgUserPassword
  - Entra ID Password Reset
publish: true
permalink: powershell/resetting-an-entra-id-users-password
---

# <span class="rune">ᚲ</span> Resetting an Entra ID User's Password

> *The same reset discipline that applies on-premises — secure input, forced change at next logon — carries over to the cloud almost unchanged. What's different is the shape of the object that carries it.*

---

## 🎯 Problem

A cloud-only or hybrid user needs a password reset through Entra ID rather than on-premises Active Directory — securely, and forcing the user to set their own permanent password at next sign-in.

More generally, this pattern answers:
> How do I reset an Entra ID user's password via Microsoft Graph, and force a change at next sign-in?

---

## 🤔 Mental Model

This is the exact same conceptual pattern as [[Resetting a User's Password via PowerShell|the on-premises AD reset]] — a secure temporary value, plus a forced-change flag — just expressed through Microsoft Graph's object shape instead of the `ActiveDirectory` module's cmdlets. The underlying discipline (never plain text, always force a change) doesn't change just because the identity system did.

---

## 🧠 Why This Pattern Works

`Update-MgUser` with a `PasswordProfile` object sets both the new password and a `ForceChangePasswordNextSignIn` flag in a single call — Microsoft Graph bundles what on-premises AD treats as two separate cmdlet calls (`Set-ADAccountPassword` and `Set-ADUser -ChangePasswordAtLogon`) into one object-based update, but the underlying intent from [[PowerShell Security Fundamentals|credential-handling discipline]] is identical: never leave a "temporary" password permanent by omission.

---

## 💻 PowerShell Solution

```powershell
Connect-MgGraph -Scopes 'User.ReadWrite.All'

$passwordProfile = @{
    Password                      = 'TempPass!2026'
    ForceChangePasswordNextSignIn = $true
}

Update-MgUser -UserId 'jdoe@contoso.com' -PasswordProfile $passwordProfile

# Confirm the account is enabled and the change actually took
Get-MgUser -UserId 'jdoe@contoso.com' -Property AccountEnabled, PasswordPolicies
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Connect with the appropriate write scope

```powershell
Connect-MgGraph -Scopes 'User.ReadWrite.All'
```

Password resets are a write operation and require a broader scope than the read-only scopes used for a simple lookup, as discussed generally in [[Connecting to Microsoft Graph or Exchange Online]].

### Step 2 — Build the password profile object

```powershell
$passwordProfile = @{ Password = 'TempPass!2026'; ForceChangePasswordNextSignIn = $true }
```

Both the new password and the forced-change behavior are set together as one object, rather than as two separate calls the way on-premises AD requires.

### Step 3 — Apply the update

```powershell
Update-MgUser -UserId 'jdoe@contoso.com' -PasswordProfile $passwordProfile
```

A single call performs the entire reset, including the forced-change flag — there's no separate follow-up step to forget here, unlike the on-premises equivalent.

---

## 🚀 Common Use Cases

- Help desk password reset for a cloud-only user with no on-premises AD account at all
- Resetting a hybrid user's password specifically through the cloud side when that's the reported point of failure
- Bulk password resets for a batch of cloud-only accounts during onboarding
- Emergency credential reset as part of an account-compromise response

---

## ⚖️ Alternatives

### Generating a random temporary password rather than a fixed one

```powershell
Add-Type -AssemblyName System.Web
$tempPassword = [System.Web.Security.Membership]::GeneratePassword(12, 3)
Update-MgUser -UserId 'jdoe@contoso.com' -PasswordProfile @{
    Password = $tempPassword
    ForceChangePasswordNextSignIn = $true
}
Write-Output "Temporary password: $tempPassword"
```

Avoids reusing the same predictable temporary password across multiple resets — the same reasoning applied in [[Resetting a User's Password via PowerShell]] for the on-premises equivalent.

### Bulk reset across a CSV of cloud-only users

```powershell
Import-Csv .\users-to-reset.csv | ForEach-Object {
    Update-MgUser -UserId $_.UserPrincipalName -PasswordProfile @{
        Password = 'TempPass!2026'
        ForceChangePasswordNextSignIn = $true
    }
}
```

---

## ⚠️ Common Mistakes

- Resetting a **hybrid** (on-premises-synced) user's password through Entra ID when password writeback isn't enabled — the change may not actually sync back on-premises, leaving the user still unable to authenticate against on-prem-dependent resources.
- Forgetting `ForceChangePasswordNextSignIn` on a bulk reset, leaving a shared temporary password permanent across many accounts — the exact same risk as its on-premises counterpart.
- Requesting `User.ReadWrite.All` broadly when a more narrowly-scoped permission would suffice for the specific task, running counter to the least-privilege intent covered in [[Connecting to Microsoft Graph or Exchange Online]].
- Not confirming whether the target user is actually cloud-only or hybrid before choosing this method — a hybrid user's authoritative password source is often still on-premises AD, making this the wrong tool entirely for that case.

---

## 💡 Wisdom from Mímir

Before resetting a password through Entra ID, confirm whether the user is cloud-only or hybrid-synced — resetting the wrong side of a hybrid identity is a common, confusing mistake where the reset appears to succeed but doesn't actually fix the user's ability to sign in.

---

## 🔗 Related Notes

- [[Microsoft 365 and Entra ID Cmdlets]]
- [[Resetting a User's Password via PowerShell]]
- [[Connecting to Microsoft Graph or Exchange Online]]
- [[Checking a User's Group and License Assignments in Entra ID]]
