---
type: powershell-pattern
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Intermediate
tags:
  - authentication
  - security-log
  - auditing
  - event-4625
aliases:
  - Event ID 4625
  - Failed Logon Audit
publish: true
permalink: powershell/auditing-failed-login-attempts-from-the-security-log
---

# <span class="rune">ᚲ</span> Auditing Failed Login Attempts from the Security Log

> *Event 4625 doesn't just say "a login failed" — its embedded status code says exactly why, and that one field is usually the difference between a five-minute investigation and an hour of guessing.*

---

## 🎯 Problem

Multiple accounts are locking out around the same time, or a specific account keeps failing to log in. Pull the actual failed-logon events to see the pattern — how many attempts, from where, and why each one failed.

More generally, this pattern answers:
> How do I query the Security log for failed logon attempts, and interpret why each one actually failed?

---

## 🤔 Mental Model

Event 4625 is like a bouncer's incident report — it doesn't just say "someone was turned away," it records exactly who they claimed to be, where they came from, and the specific reason they were refused (wrong password, expired account, disabled account, time restriction). Reading only the surface-level "logon failed" without checking the embedded status/sub-status code is like reading the bouncer's report and stopping after the first sentence.

---

## 🧠 Why This Pattern Works

Every failed logon on Windows generates event ID 4625 in the Security log, carrying structured properties including the target account name, source machine/IP, and — critically — a `Status`/`SubStatus` code pair that identifies the specific failure reason (`0xC000006A` = wrong password, `0xC0000234` = account locked out, `0xC0000072` = account disabled, `0xC0000193` = account expired). As covered generally in [[Event Log Cmdlets]], `-FilterHashtable` is essential here — a busy DC's Security log can contain millions of entries, and filtering by `Id=4625` server-side rather than pulling everything is the only practical way to query it.

---

## 💻 PowerShell Solution

```powershell
# Failed logons in the last 24 hours, with the actual failure reason decoded
$statusCodes = @{
    '0xC000006A' = 'Incorrect password'
    '0xC0000234' = 'Account locked out'
    '0xC0000072' = 'Account disabled'
    '0xC0000193' = 'Account expired'
    '0xC0000071' = 'Password expired'
    '0xC0000070' = 'Restricted to specific workstations'
}

Get-WinEvent -FilterHashtable @{
    LogName   = 'Security'
    Id        = 4625
    StartTime = (Get-Date).AddDays(-1)
} | ForEach-Object {
    $subStatus = '0x{0:X8}' -f $_.Properties[9].Value
    [pscustomobject]@{
        Time        = $_.TimeCreated
        Account     = $_.Properties[5].Value
        SourceIP    = $_.Properties[19].Value
        Reason      = $statusCodes[$subStatus]
        RawSubStatus = $subStatus
    }
} | Sort-Object Time
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Filter efficiently at the source

```powershell
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4625; StartTime=(Get-Date).AddDays(-1)}
```

Only pulls 4625 events from the last day, avoiding a full scan of the entire Security log.

### Step 2 — Extract the target account and source

```powershell
$_.Properties[5].Value   # Account Name
$_.Properties[19].Value  # Source Network Address
```

4625 event properties are positional, not named — the specific index for each field is consistent for a given Windows version, but worth confirming with a sample event (`.Properties | ForEach-Object {$_.Value}`) if behavior looks off after a major OS update.

### Step 3 — Decode the sub-status into a human-readable reason

```powershell
$subStatus = '0x{0:X8}' -f $_.Properties[9].Value
$statusCodes[$subStatus]
```

The sub-status code is what actually distinguishes "wrong password" from "account locked" from "account disabled" — all of which otherwise look identical as a generic "logon failure" without decoding this field.

---

## 🚀 Common Use Cases

- Investigating the source of repeated account lockouts, as the natural follow-up to [[Finding and Unlocking a Locked-Out AD Account]]
- Detecting a possible password-spray attack (many different accounts, few attempts each, from the same source)
- Confirming whether a "can't log in" complaint is actually a wrong password, an expired password, or a disabled account
- Building a recurring security report of failed logon patterns across domain controllers

---

## ⚖️ Alternatives

### Querying across all domain controllers, not just one

```powershell
$dcs = (Get-ADDomainController -Filter *).HostName
$dcs | ForEach-Object {
    Get-WinEvent -ComputerName $_ -FilterHashtable @{LogName='Security'; Id=4625; StartTime=(Get-Date).AddHours(-2)} -ErrorAction SilentlyContinue
}
```

Failed logon attempts can be processed by any DC that received the request, not necessarily the PDC Emulator (unlike lockouts specifically) — a thorough investigation across a multi-DC environment needs to check each one.

### Grouping by source to spot a pattern

```powershell
$events | Group-Object SourceIP | Sort-Object Count -Descending | Select-Object Name, Count
```

Aggregating failed attempts by source IP quickly surfaces whether one machine is responsible for a disproportionate share of failures — a strong signal of a misconfigured service, stale cached credential, or genuine attack.

---

## ⚠️ Common Mistakes

- Reading only the generic "An account failed to log on" message text and not decoding the sub-status code, missing the actual reason (which is usually the entire point of the investigation).
- Querying only the PDC Emulator for failed logons, assuming (incorrectly) that failures are centralized the same way lockouts are — failed attempts can land on whichever DC processed that specific request.
- Using `Where-Object` after retrieving the entire unfiltered Security log instead of `-FilterHashtable`, resulting in a painfully slow query on any busy production DC.
- Confusing the "Account Name" field with the "Account Domain" field — a poorly-formed or spoofed logon attempt can sometimes populate these inconsistently, and treating them uncritically can misattribute an attempt to the wrong account.

---

## 📖 Further Reading

- Microsoft Learn: "Event 4625(F): An account failed to log on" (full sub-status code reference)

---

## 💡 Wisdom from Mímir

Never report a failed-logon investigation as "logon failures on the account" without the decoded sub-status — "wrong password 40 times from one IP" and "account was already disabled 40 times" tell completely different stories, and the raw event text alone doesn't distinguish them.

---

## 🔗 Related Notes

- [[Event Log Cmdlets]]
- [[Finding and Unlocking a Locked-Out AD Account]]
- [[Testing Credentials Without Locking an Account]]
- [[Reading the Last N System Errors from the Event Log]]
