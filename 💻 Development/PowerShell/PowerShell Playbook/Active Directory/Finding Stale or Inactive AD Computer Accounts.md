---
type: powershell-pattern
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Intermediate
tags:
  - active-directory
  - computer-accounts
  - cleanup
  - auditing
aliases:
  - Stale Computer Accounts
  - AD Computer Cleanup
publish: true
permalink: powershell/finding-stale-or-inactive-ad-computer-accounts
---

# <span class="rune">ᚲ</span> Finding Stale or Inactive AD Computer Accounts

> *A computer account doesn't announce its own retirement — it just stops checking in, and quietly keeps existing, still counted, still a potential attack surface, until someone actually goes looking for the silence.*

---

## 🎯 Problem

The directory has accumulated years of decommissioned laptops and retired servers whose computer accounts were never cleaned up. Find them so they can be disabled or removed.

More generally, this pattern answers:
> Which computer accounts in AD haven't actually authenticated in a long time, and are therefore likely stale?

---

## 🤔 Mental Model

Every computer account periodically "checks in" with the domain by resetting its own machine password (by default, every 30 days) and updating its `lastLogonTimestamp`. A retired machine simply stops doing that — there's no explicit "I'm being decommissioned" signal, just an ever-growing gap since the last check-in. Finding stale accounts means picking a silence threshold (90 days is a common baseline) and asking the directory which accounts have gone quiet longer than that.

---

## 🧠 Why This Pattern Works

`lastLogonTimestamp` is a domain-wide, replicated attribute approximating when an account last authenticated — unlike the older, non-replicated `lastLogon` attribute, which only reflects the *specific* domain controller queried and can undercount significantly across a multi-DC environment. Filtering `Search-ADAccount -ComputersOnly -AccountInactive -TimeSpan` (or an equivalent `-Filter` query against `lastLogonTimestamp`) against a threshold surfaces every computer account that hasn't reset its password or authenticated within that window — a strong practical signal the underlying machine no longer exists or was never rejoined after being wiped.

---

## 💻 PowerShell Solution

```powershell
# Computer accounts inactive for 90+ days
Search-ADAccount -ComputersOnly -AccountInactive -TimeSpan 90.00:00:00 |
    Select-Object Name, LastLogonDate, Enabled |
    Sort-Object LastLogonDate

# Same idea, built manually with Get-ADComputer for more control over properties
$cutoff = (Get-Date).AddDays(-90)
Get-ADComputer -Filter {LastLogonTimestamp -lt $cutoff} -Properties LastLogonDate, OperatingSystem |
    Select-Object Name, LastLogonDate, OperatingSystem |
    Sort-Object LastLogonDate
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Define the inactivity threshold

```powershell
$cutoff = (Get-Date).AddDays(-90)
```

90 days is a common, conservative baseline — long enough to rule out machines that were simply off during a vacation or extended remote period, short enough to catch genuinely abandoned accounts within a reasonable audit cycle.

### Step 2 — Query for accounts past that threshold

```powershell
Search-ADAccount -ComputersOnly -AccountInactive -TimeSpan 90.00:00:00
```

`Search-ADAccount` handles the `lastLogonTimestamp` comparison internally — its `-TimeSpan` parameter expresses the threshold as `days.hours:minutes:seconds`.

### Step 3 — Review before acting

```powershell
Select-Object Name, LastLogonDate, Enabled
```

Always review the resulting list — including whether accounts are already disabled — before bulk-disabling or deleting anything, since a legitimately provisioned-but-rarely-used machine (an emergency spare, a seasonal kiosk) can look identical to a genuinely abandoned one from this data alone.

---

## 🚀 Common Use Cases

- Periodic AD hygiene/cleanup audits to reduce directory clutter and attack surface
- Identifying computer accounts eligible for disabling before outright deletion (a safer two-step cleanup process)
- Investigating unexpectedly high computer account counts relative to known active device inventory
- Feeding a stale-account report into a broader asset lifecycle/decommissioning process

---

## ⚖️ Alternatives

### Two-step safe cleanup — disable first, delete later

```powershell
$staleComputers = Search-ADAccount -ComputersOnly -AccountInactive -TimeSpan 90.00:00:00
$staleComputers | Disable-ADAccount

# weeks later, after confirming nothing broke:
# $staleComputers | Remove-ADObject -Confirm:$true
```

Disabling first (rather than deleting immediately) gives a safety window to catch any account that turns out to still be needed, without the finality of an outright deletion.

### Cross-referencing against a known-active device inventory

```powershell
$staleComputers = Search-ADAccount -ComputersOnly -AccountInactive -TimeSpan 90.00:00:00
$knownActive = Import-Csv .\active-inventory.csv
$staleComputers | Where-Object { $_.Name -notin $knownActive.ComputerName }
```

Reduces false positives by excluding machines already confirmed active through an independent inventory source (e.g., SCCM or an asset management system), rather than relying purely on `lastLogonTimestamp`.

---

## ⚠️ Common Mistakes

- Using `lastLogon` instead of `lastLogonTimestamp` for a domain-wide stale check — `lastLogon` is per-DC and not replicated, so a machine that happened to authenticate against a *different* DC than the one queried can look far staler than it actually is.
- Deleting stale computer accounts outright without a disable-first grace period, permanently losing the account's SID and any group memberships if the machine turns out to still be in occasional use.
- Choosing too short an inactivity threshold (e.g., 14 days) and flagging legitimately active machines that were simply offline during an extended absence.
- Not accounting for `lastLogonTimestamp`'s intentional replication delay (up to ~14 days by default) — treating the attribute as perfectly real-time can make a recently-inactive machine look slightly staler than it truly is right at the threshold boundary.

---

## 💡 Wisdom from Mímir

Always disable before you delete. A stale computer account is cheap to keep disabled for a few weeks as a safety net — but once deleted, its SID and every permission tied to it are gone permanently, with no way to simply "undo" the cleanup if it turns out to have been premature.

---

## 🔗 Related Notes

- [[Active Directory Cmdlets]]
- [[Finding All Members of an AD Group]]
- [[Checking Windows Update History and Pending Updates]]
- [[Finding a Device's SCCM Collection Membership]]
