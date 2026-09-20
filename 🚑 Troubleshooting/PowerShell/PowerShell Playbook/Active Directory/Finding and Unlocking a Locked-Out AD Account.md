---
type: powershell-pattern
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Beginner
tags:
  - active-directory
  - account-lockout
  - help-desk
aliases:
  - Unlock-ADAccount
  - AD Account Lockout
publish: true
permalink: powershell/finding-and-unlocking-a-locked-out-ad-account
---

# <span class="rune">ᚲ</span> Finding and Unlocking a Locked-Out AD Account

> *A lockout is never really about the account — it's about whatever's still quietly retrying the old password somewhere. Unlocking without finding that source just buys a few more minutes before the ticket comes back.*

---

## 🎯 Problem

A user calls in locked out of their account. Unlock them, and figure out where the bad password attempts are actually coming from before it happens again.

More generally, this pattern answers:
> How do I find which account(s) are currently locked out, unlock one, and identify the source of repeated bad authentication attempts?

---

## 🤔 Mental Model

Think of an AD account lockout like a circuit breaker tripping. Flipping the breaker back on (`Unlock-ADAccount`) takes ten seconds and fixes the symptom immediately — but if something plugged into that circuit is still shorting out, the breaker trips again the moment you walk away. The real fix is tracing which "device" (an old cached credential on a phone, a mapped drive, a scheduled task) is still hammering the account with a stale password, using the domain controller's Security log as the trail.

---

## 🧠 Why This Pattern Works

`Search-ADAccount -LockedOut` queries the domain directly for the `lockoutTime` attribute being set on any account — no guessing which user is affected. `Unlock-ADAccount` simply clears that attribute, restoring access instantly. But the lockout itself is only ever a *symptom*: Windows locks an account after N failed password attempts within a policy-defined window, and every one of those attempts is recorded as event ID 4625 in the Security log of the domain controller that actually processed the authentication attempt — which is why finding the real DC that saw the failures (not just any DC) is the key step in tracing the source.

---

## 💻 PowerShell Solution

```powershell
# Step 1: Find all currently locked-out accounts
Search-ADAccount -LockedOut | Select-Object Name, SamAccountName, LockedOut

# Step 2: Unlock the specific user
Unlock-ADAccount -Identity 'jdoe'

# Step 3: Find which DC actually processed the lockout, then check its Security log for the source
$user = Get-ADUser -Identity 'jdoe' -Properties LockedOut, LastBadPasswordAttempt
$pdcEmulator = (Get-ADDomain).PDCEmulator

Get-WinEvent -ComputerName $pdcEmulator -FilterHashtable @{
    LogName = 'Security'
    Id      = 4625
    StartTime = (Get-Date).AddHours(-2)
} | Where-Object { $_.Properties[5].Value -eq 'jdoe' } |
    Select-Object TimeCreated, @{N='SourceComputer';E={$_.Properties[13].Value}}
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Identify every locked-out account domain-wide

```powershell
Search-ADAccount -LockedOut
```

Queries the directory for any account currently flagged as locked out — useful both for a single reported user and for spotting a wider pattern (many accounts locking out at once often points to a shared cause, like a service account password rotation nobody updated everywhere).

### Step 2 — Clear the lockout

```powershell
Unlock-ADAccount -Identity 'jdoe'
```

Resets the `lockoutTime` attribute, immediately restoring the user's ability to log in.

### Step 3 — Trace the source of the failed attempts

```powershell
$pdcEmulator = (Get-ADDomain).PDCEmulator
Get-WinEvent -ComputerName $pdcEmulator -FilterHashtable @{LogName='Security'; Id=4625; StartTime=(Get-Date).AddHours(-2)}
```

Lockouts are always evaluated against the **PDC Emulator** FSMO role holder, regardless of which DC actually authenticated the failed attempt — so the failed-attempt events themselves might be scattered across several DCs, but checking the PDC Emulator's own log first is the fastest way to confirm the lockout actually happened and roughly when.

---

## 🚀 Common Use Cases

- Help desk unlocking a user reporting "my account is locked"
- Investigating why the *same* user keeps getting locked out repeatedly (usually a stale cached credential somewhere)
- Spotting a mass-lockout event across many accounts simultaneously (often a signal of a password-spray attack or a service account credential rotation gone wrong)
- Confirming a lockout has actually cleared after `Unlock-ADAccount`, rather than assuming success

---

## ⚖️ Alternatives

### Checking lockout status without unlocking

```powershell
Get-ADUser -Identity 'jdoe' -Properties LockedOut, LastBadPasswordAttempt, BadLogonCount
```

Useful when the ticket asks "is this account actually locked" before committing to an unlock — sometimes a user is confusing a lockout with a simple wrong-password screen.

### Unlocking across every DC at once (for multi-DC environments with replication lag)

```powershell
(Get-ADDomainController -Filter *).Name | ForEach-Object {
    Unlock-ADAccount -Identity 'jdoe' -Server $_
}
```

In environments with slow AD replication, an unlock applied to only one DC can appear to "not work" if the user's next authentication attempt lands on a DC that hasn't replicated the unlock yet. Applying the unlock against every DC individually sidesteps replication lag entirely.

---

## ⚠️ Common Mistakes

- Unlocking the account without checking the Security log for the source, closing the ticket, and having the exact same lockout recur an hour later from the same stale credential.
- Assuming the DC you happen to be connected to is the one holding the authoritative lockout state — the PDC Emulator role always arbitrates lockouts, regardless of which DC you're querying from.
- Confusing `LockedOut` with `Enabled` — a disabled account is a completely different problem from a locked-out one, and `Unlock-ADAccount` does nothing for a disabled account.
- Not checking `BadPasswordTime`/event 4625 timestamps against known scheduled tasks or mapped drives using the old password, which is one of the most common recurring sources.

---

## 💡 Wisdom from Mímir

Never treat `Unlock-ADAccount` as the fix — treat it as the pause button. The real fix is whatever event 4625 on the PDC Emulator points to: an old phone, a forgotten mapped drive, or a scheduled task still holding a stale password.

---

## 🔗 Related Notes

- [[Active Directory Cmdlets]]
- [[Event Log Cmdlets]]
- [[Auditing Failed Login Attempts from the Security Log]]
- [[Resetting a User's Password via PowerShell]]
