---
type: powershell-pattern
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Beginner
tags:
  - authentication
  - sessions
  - remote-desktop
aliases:
  - quser PowerShell
  - Active Logon Sessions
publish: true
permalink: powershell/checking-a-users-current-logon-session
---

# <span class="rune">ᚲ</span> Checking a User's Current Logon Session

> *"Are they logged in?" has three different honest answers — actively, disconnected-but-still-holding-a-session, or not at all — and only checking sessions actually tells them apart.*

---

## 🎯 Problem

A user says they can't log into a shared workstation or terminal server. Before troubleshooting further, confirm whether they (or someone else) already have an active or disconnected session tying up a slot.

More generally, this pattern answers:
> Who is currently logged into this machine, and is their session active or just disconnected?

---

## 🤔 Mental Model

A disconnected RDP session is like a hotel room where the guest stepped out but never checked out — the room (and its resources) stays reserved and occupied even though nobody's physically there. `quser` is the front desk's registry: it tells you exactly which rooms are occupied, which are "checked out but door still locked" (disconnected), and which are free — information a simple "can they log in" question can't answer without it.

---

## 🧠 Why This Pattern Works

Windows tracks interactive and RDP sessions distinctly from simple process ownership — a session can be `Active` (someone's actively connected and interacting) or `Disc` (disconnected, but the session and everything running in it, like a locked application, are still alive and consuming resources). `quser`/`query user` reads this session table directly, which is why it remains the standard tool for this question even though it predates PowerShell's object model, as covered in [[User and Session Cmdlets]].

---

## 💻 PowerShell Solution

```powershell
# Local machine — who's logged in right now
quser

# Remote machine
quser /server:srv01

# Wrapping quser output into structured objects for filtering/scripting
function Get-LoggedOnUser {
    param([string]$ComputerName = $env:COMPUTERNAME)

    quser /server:$ComputerName 2>$null | Select-Object -Skip 1 | ForEach-Object {
        $line = $_ -replace '\s{2,}', ',' -split ','
        [pscustomobject]@{
            UserName    = $line[0].TrimStart('>')
            SessionName = $line[1]
            Id          = $line[2]
            State       = $line[3]
            IdleTime    = $line[4]
            LogonTime   = $line[5]
        }
    }
}

Get-LoggedOnUser -ComputerName 'srv01'
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Query sessions on the target machine

```powershell
quser /server:srv01
```

Returns a fixed-width text table: username, session name, session ID, state (Active/Disc), idle time, and logon time.

### Step 2 — Parse the fixed-width text into usable objects

```powershell
$line = $_ -replace '\s{2,}', ',' -split ','
```

Since `quser` predates PowerShell's object model, its output needs manual parsing — collapsing runs of 2+ spaces into a single delimiter is a reliable, simple way to split its columns without depending on exact character positions.

### Step 3 — Filter or act on the structured result

```powershell
Get-LoggedOnUser -ComputerName 'srv01' | Where-Object State -eq 'Disc'
```

Once wrapped as objects, standard [[Filtering and Comparison Operators|filtering]] applies — e.g., isolating only disconnected sessions that might be safe to force-close.

---

## 🚀 Common Use Cases

- Checking why a user can't log into a shared/terminal server ("maximum sessions exceeded" style issues)
- Confirming whether a disconnected session is quietly consuming a license or resource slot on a terminal server
- Identifying who's logged into a machine before performing maintenance that would disrupt an active user
- Auditing which sessions across a fleet of servers have been idle or disconnected for an unusually long time

---

## ⚖️ Alternatives

### CIM-based approach (fully object-based, no text parsing)

```powershell
Get-CimInstance -ClassName Win32_LoggedOnUser -ComputerName 'srv01' |
    Select-Object -ExpandProperty Antecedent -Unique
```

Avoids text parsing entirely, but `Win32_LoggedOnUser` requires an associative query to interpret cleanly and doesn't distinguish Active vs. Disconnected state as directly as `quser` does — often more complex for the same basic answer, as noted in [[User and Session Cmdlets]].

### Checking session count against a known server limit

```powershell
$sessions = quser /server:srv01 2>$null
if (($sessions | Measure-Object).Count - 1 -ge 2) {
    Write-Warning "srv01 is at or near its session limit"
}
```

Useful on servers with a hard cap on concurrent sessions (common on non-server Windows editions used for RDP, which limit to a small number of concurrent sessions).

---

## ⚠️ Common Mistakes

- Running plain `quser` and assuming it checked the remote machine, when omitting `/server:<name>` silently checks the *local* machine's sessions instead.
- Force-closing a disconnected session without confirming it's actually stale — a legitimately disconnected-but-still-working session (someone stepped away temporarily) can lose unsaved work if ended.
- Assuming `quser`'s column positions are fixed-width in a way safe to slice by character index — the space-collapsing parse approach is more robust across different username lengths.
- Treating `Win32_LoggedOnUser`/`Win32_LogonSession` CIM results as a drop-in replacement for `quser` without accounting for the additional associative-query complexity needed to get equivalent Active/Disconnected state.

---

## 💡 Wisdom from Mímir

Before troubleshooting a "can't log in" ticket any further, check sessions first — a surprising share of these tickets resolve the moment a stale disconnected session is found and closed, with no other troubleshooting needed at all.

---

## 🔗 Related Notes

- [[User and Session Cmdlets]]
- [[Managing Local Administrator Group Membership]]
- [[Testing Credentials Without Locking an Account]]
- [[Finding and Killing a Frozen Process]]
