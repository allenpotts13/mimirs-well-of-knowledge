---
type: concept
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Intermediate
tags:
  - powershell-domain-reference
  - active-directory
aliases:
  - PowerShell AD Module
  - ActiveDirectory Module
publish: true
permalink: powershell/active-directory-cmdlets
---

# <span class="rune">ᛟ</span> Active Directory Cmdlets

> *The AD module turns a directory of thousands of objects into something you can query, filter, and act on with the same object-and-pipeline discipline as everything else in PowerShell.*

---

## 🎯 Purpose

The `ActiveDirectory` module (installed via RSAT — Remote Server Administration Tools) provides cmdlets for querying and managing users, groups, computers, and organizational units directly from PowerShell, replacing GUI tools like Active Directory Users and Computers for anything that needs to be repeatable, bulk, or scripted.

For troubleshooting specifically, this module is the backbone of nearly every account-related investigation: lockouts, stale accounts, group membership questions, and password issues almost always start with an `ActiveDirectory` cmdlet.

---

## 🧠 Key Ideas

- `Get-ADUser`, `Get-ADGroup`, `Get-ADComputer` retrieve directory objects; `-Filter` (not `Where-Object`) is the efficient way to query the directory server-side rather than pulling everything and filtering client-side.
- `-Properties` must be specified explicitly to return anything beyond a small default property set — a very common source of "why is this property empty" confusion (`Get-ADUser jdoe -Properties LastLogonDate` vs. plain `Get-ADUser jdoe`).
- `Set-ADUser`, `Set-ADAccountPassword`, `Unlock-ADAccount`, `Add-ADGroupMember` are the corresponding action cmdlets for making changes, following the same `Get-`/`Set-`/verb-noun pattern as the rest of PowerShell.
- `-Identity` accepts a SamAccountName, distinguished name, GUID, or SID — flexible, but means an "identity not found" error can stem from any of several different lookup types failing.
- `-Filter` uses a PowerShell-like syntax (not full WQL) — e.g., `Get-ADUser -Filter "Name -like '*smith*'"` — distinct from the `-LDAPFilter` parameter, which accepts raw LDAP query syntax for more complex queries.

---

## ⚙️ How It Works

AD cmdlets query the directory through a domain controller, typically over LDAP under the hood. `-Filter` translates into a server-side query, meaning only matching objects cross the network — critical for performance in large directories where `Get-ADUser -Filter *` alone can return tens of thousands of objects. The AD module's default property set deliberately excludes many attributes (like `LastLogonDate`, `PasswordLastSet`, `MemberOf`) for performance reasons; each of those requires the `-Properties` parameter to actually be retrieved, even though they always exist on the underlying object.

```text
Get-ADUser -Filter "Enabled -eq `$false`" -Properties LastLogonDate
        ↓
   query sent to domain controller (server-side filtering)
        ↓
   only DISABLED accounts returned, each with LastLogonDate populated
   (would be empty without -Properties, even though it always exists)
```

---

## 💻 Examples

```powershell
# Basic lookup — limited default properties
Get-ADUser -Identity jdoe

# Explicitly requesting extra properties
Get-ADUser -Identity jdoe -Properties LastLogonDate, PasswordLastSet, MemberOf

# Server-side filtering instead of pulling everything
Get-ADUser -Filter "Department -eq 'IT'" -Properties Department

# Finding locked-out accounts
Search-ADAccount -LockedOut | Select-Object Name, SamAccountName

# Unlocking an account
Unlock-ADAccount -Identity jdoe

# Adding a user to a group
Add-ADGroupMember -Identity 'Help Desk' -Members jdoe
```

---

## 🚀 Real World Applications

- Investigating account lockouts with `Search-ADAccount -LockedOut`
- Auditing stale computer or user accounts by `LastLogonDate` for cleanup
- Bulk-adding or removing group memberships during onboarding/offboarding
- Resetting passwords and forcing a change at next logon during help desk tickets

---

## ⚖️ Advantages

- Server-side `-Filter` scales cleanly to directories with tens of thousands of objects.
- Every AD cmdlet follows the same Verb-Noun and pipeline conventions as the rest of PowerShell, so AD administration composes naturally with filtering, exporting, and looping.
- Bulk operations (CSV-driven user creation, mass group updates) that would take hours in a GUI take seconds.
- `-Identity` accepting multiple lookup formats (SamAccountName, DN, GUID, SID) makes scripts flexible regardless of what identifier is on hand.

---

## ⚠️ Limitations

- Requires RSAT installed and appropriate AD permissions — not available on a machine without the tools or without directory read/write rights.
- The default limited property set is a frequent trap for anyone not yet aware `-Properties` must be requested explicitly.
- `-Filter` syntax looks like PowerShell but isn't identical — some operators and property names behave differently than expected coming from `Where-Object` habits.
- Bulk write operations (bulk password resets, bulk group changes) can affect large numbers of accounts quickly if a filter is too broad — no built-in confirmation beyond what a script author adds.

---

## 🚨 Common Mistakes

- Querying `Get-ADUser -Identity jdoe | Select LastLogonDate` and getting nothing, because `-Properties LastLogonDate` was never requested on the original `Get-ADUser` call.
- Using `Get-ADUser -Filter * | Where-Object {...}` instead of putting the condition directly in `-Filter`, pulling the entire directory across the network unnecessarily.
- Confusing `-Filter` (PowerShell-like syntax) with `-LDAPFilter` (raw LDAP syntax) and mixing their syntax incorrectly.
- Running a bulk `Set-ADUser`/`Add-ADGroupMember` operation against a filtered set without first previewing the filter's results, risking changes to more accounts than intended.

---

## 📖 Further Reading

- `Get-Help Get-ADUser -Full`
- `Get-Command -Module ActiveDirectory`
- Microsoft Learn: "Active Directory module for Windows PowerShell"

---

## 💡 Wisdom from Mímir

Before running any bulk AD write operation, run the exact same `-Filter` through `Get-AD*` first and eyeball the results. A filter that's slightly too broad is invisible until it's already made the change.

---

## 🔗 Related Notes

- [[Modules]]
- [[Finding and Unlocking a Locked-Out AD Account]]
- [[Microsoft 365 and Entra ID Cmdlets]]
- [[PowerShell Codex]]
