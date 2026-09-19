---
type: powershell-pattern
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Beginner
tags:
  - active-directory
  - password-reset
  - help-desk
aliases:
  - Set-ADAccountPassword
  - AD Password Reset
publish: true
permalink: powershell/resetting-a-users-password-via-powershell
---

# <span class="rune">ᚲ</span> Resetting a User's Password via PowerShell

> *`Set-ADAccountPassword` changes what the account holds. `-ChangePasswordAtLogon` decides whether that new password is temporary or permanent the moment it's typed. Confusing the two is how "reset passwords" tickets turn into "reset it again" tickets.*

---

## 🎯 Problem

A user has forgotten their password and needs a reset — securely, without ever displaying the new password in plain text on screen or in a script's output.

More generally, this pattern answers:
> How do I reset an AD user's password from PowerShell using a securely-entered value, and force them to choose their own new password at next logon?

---

## 🤔 Mental Model

Think of the reset as two independent decisions bundled into one ticket: **what** the temporary password is, and **whether the user is trusted to keep it**. `Set-ADAccountPassword` only answers the first question — it's just a value swap. `-ChangePasswordAtLogon $true` answers the second — it flags the account so that value is treated as a one-time key, forcing the user to pick their own permanent password the moment they log in with it. Skipping that flag means the "temporary" password quietly becomes permanent, which is rarely the actual intent of a help-desk reset.

---

## 🧠 Why This Pattern Works

`Set-ADAccountPassword` requires a `SecureString`, not plain text — this is PowerShell's [[PowerShell Security Fundamentals|credential-handling]] discipline applied directly to AD: the new password is never held as readable text in a variable, on screen, or in a transcript log if `Read-Host -AsSecureString` is used to capture it. `Set-ADUser -ChangePasswordAtLogon $true` sets the underlying `pwdLastSet` attribute to `0`, which is the actual mechanism Windows uses to force a password change at the very next interactive logon — it's a distinct, separate call from the password reset itself, which is exactly why it's easy to forget.

---

## 💻 PowerShell Solution

```powershell
# Prompt securely for the new temporary password — never appears in plain text
$newPassword = Read-Host -Prompt 'Enter temporary password' -AsSecureString

Set-ADAccountPassword -Identity 'jdoe' -Reset -NewPassword $newPassword

# Force the user to set their own password at next logon
Set-ADUser -Identity 'jdoe' -ChangePasswordAtLogon $true

# If the account was also locked out from repeated bad attempts, clear that too
Unlock-ADAccount -Identity 'jdoe'
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Capture the new password securely

```powershell
$newPassword = Read-Host -Prompt 'Enter temporary password' -AsSecureString
```

`-AsSecureString` masks the input on screen and stores it as an encrypted `SecureString` object in memory — never as plain, readable text.

### Step 2 — Reset the password

```powershell
Set-ADAccountPassword -Identity 'jdoe' -Reset -NewPassword $newPassword
```

`-Reset` explicitly signals this is an administrative reset (bypassing the old password requirement), rather than a self-service change requiring the current password.

### Step 3 — Force the user to change it at next logon

```powershell
Set-ADUser -Identity 'jdoe' -ChangePasswordAtLogon $true
```

Without this step, the temporary password silently becomes the user's permanent password — a separate, easy-to-forget flag.

---

## 🚀 Common Use Cases

- Standard help-desk password reset workflow for a forgotten-password ticket
- Bulk password resets across multiple new-hire accounts before their first day
- Resetting a service or shared account's password as part of scheduled credential rotation
- Combining a reset with an unlock when a user was locked out AND needs a new password

---

## ⚖️ Alternatives

### Generating a random temporary password instead of typing one manually

```powershell
Add-Type -AssemblyName System.Web
$plainTemp = [System.Web.Security.Membership]::GeneratePassword(12, 3)
$newPassword = ConvertTo-SecureString -String $plainTemp -AsPlainText -Force

Set-ADAccountPassword -Identity 'jdoe' -Reset -NewPassword $newPassword
Write-Output "Temporary password for jdoe: $plainTemp"   # display once, then discard
```

Useful for scripted bulk resets where a human isn't typing each password interactively — note the temporary password still needs to be communicated to the user through a separate secure channel, not left in a script's output history.

### Bulk reset across a CSV of usernames

```powershell
Import-Csv .\users-to-reset.csv | ForEach-Object {
    $temp = ConvertTo-SecureString -String 'TempPass!2026' -AsPlainText -Force
    Set-ADAccountPassword -Identity $_.SamAccountName -Reset -NewPassword $temp
    Set-ADUser -Identity $_.SamAccountName -ChangePasswordAtLogon $true
}
```

---

## ⚠️ Common Mistakes

- Forgetting `-ChangePasswordAtLogon $true`, leaving the "temporary" password as the account's permanent one indefinitely.
- Passing a plain-text string directly where a `SecureString` is expected — this throws a type error, and reaching for `ConvertTo-SecureString -AsPlainText -Force` as a workaround without realizing the plain text still briefly existed in a variable or script file.
- Using `Set-ADAccountPassword` without `-Reset` when performing an administrative reset — without it, the cmdlet expects to also validate the *old* password, which an administrator resetting someone else's forgotten password doesn't have.
- Hardcoding the same temporary password across many bulk-reset accounts without also forcing `-ChangePasswordAtLogon`, creating a real security exposure if any of those temporary passwords are never actually changed.

---

## 💡 Wisdom from Mímir

Treat `Set-ADAccountPassword` and `Set-ADUser -ChangePasswordAtLogon` as one inseparable pair of commands for a help-desk reset — running the first without the second doesn't fail loudly, it just quietly leaves a "temporary" password permanent.

---

## 🔗 Related Notes

- [[Active Directory Cmdlets]]
- [[PowerShell Security Fundamentals]]
- [[Finding and Unlocking a Locked-Out AD Account]]
- [[Bulk-Creating AD Users from a CSV]]
