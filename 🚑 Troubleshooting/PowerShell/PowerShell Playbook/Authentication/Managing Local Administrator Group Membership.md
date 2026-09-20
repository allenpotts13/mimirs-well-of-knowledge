---
type: powershell-pattern
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Beginner
tags:
  - authentication
  - local-accounts
  - privilege-management
aliases:
  - Get-LocalGroupMember Administrators
  - Local Admin Audit
publish: true
permalink: powershell/managing-local-administrator-group-membership
---

# <span class="rune">ᚲ</span> Managing Local Administrator Group Membership

> *"Who's a local admin on this machine" and "who's a domain admin" are entirely different questions — this one is about the SAM database on one specific box, and it's usually the more interesting one during an incident.*

---

## 🎯 Problem

Confirm exactly who has local administrator rights on a specific machine — and remove someone who shouldn't be there, without disrupting legitimate access.

More generally, this pattern answers:
> Who is currently a member of the local Administrators group on this machine, and how do I safely add or remove someone?

---

## 🤔 Mental Model

The local Administrators group is like the key ring for one specific building, separate entirely from whatever master keys the domain hands out. A user can be a domain user with no special rights anywhere else, yet still hold a key to this one building's front door if someone added them to *this machine's* local group directly — which is exactly the scenario this pattern exists to audit and correct.

---

## 🧠 Why This Pattern Works

`Get-LocalGroupMember`/`Add-LocalGroupMember`/`Remove-LocalGroupMember` operate purely against the local SAM database, as covered generally in [[User and Session Cmdlets]] — entirely independent of Active Directory group membership. This distinction matters directly for security: local admin rights granted directly on a machine (rather than through a managed, auditable domain group like a "Workstation Admins" AD group added to the local Administrators group via GPO) are far easier to lose track of over time, and are a common privilege-creep finding during security reviews.

---

## 💻 PowerShell Solution

```powershell
# Who's currently a local administrator on this machine
Get-LocalGroupMember -Group 'Administrators'

# Same check against a remote machine
Invoke-Command -ComputerName 'ws042' -ScriptBlock {
    Get-LocalGroupMember -Group 'Administrators'
}

# Remove a user who shouldn't have local admin rights
Remove-LocalGroupMember -Group 'Administrators' -Member 'CONTOSO\jdoe'

# Add a domain group (the preferred, auditable approach) instead of an individual user
Add-LocalGroupMember -Group 'Administrators' -Member 'CONTOSO\Workstation Admins'
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Enumerate current local admin membership

```powershell
Get-LocalGroupMember -Group 'Administrators'
```

Returns each member's name and whether it's a local account, domain user, or domain group — an important distinction for spotting an individually-added domain user versus a properly managed domain group.

### Step 2 — Remove an inappropriate member

```powershell
Remove-LocalGroupMember -Group 'Administrators' -Member 'CONTOSO\jdoe'
```

Removes the specific access grant without touching any other membership — a targeted correction rather than a full group reset.

### Step 3 — Prefer a managed group over individual accounts going forward

```powershell
Add-LocalGroupMember -Group 'Administrators' -Member 'CONTOSO\Workstation Admins'
```

Granting access through a domain group that's centrally managed (and ideally deployed via GPO Restricted Groups or Group Policy Preferences) is far more auditable than accumulating individually-added local admin exceptions machine by machine.

---

## 🚀 Common Use Cases

- Auditing local admin membership across a fleet of machines as part of a security review
- Investigating unexpected local privilege during an incident response ("how did this user get admin rights on this box specifically")
- Cleaning up accumulated ad-hoc local admin grants that built up over years of one-off help desk fixes
- Standardizing local admin access onto a managed domain group instead of individually-added accounts

---

## ⚖️ Alternatives

### Auditing across an entire fleet via remoting

```powershell
$computers = 'ws001','ws002','ws042'
Invoke-Command -ComputerName $computers -ScriptBlock {
    [pscustomobject]@{
        ComputerName = $env:COMPUTERNAME
        Members      = (Get-LocalGroupMember -Group 'Administrators').Name -join '; '
    }
} | Select-Object ComputerName, Members
```

Scales the same check across many machines at once using [[Remoting]], producing a single consolidated audit report.

### Legacy `net localgroup` equivalent (for reference/compatibility)

```powershell
net localgroup Administrators
```

Still works and predates `Get-LocalGroupMember`, but returns plain text rather than structured objects — useful only as a quick manual check, not for scripting.

---

## ⚠️ Common Mistakes

- Removing a member from local Administrators without confirming it isn't a service account or application dependency relying on that access, causing an unexpected outage.
- Adding individual domain users directly to the local Administrators group instead of a managed domain group, recreating the exact privilege-sprawl problem this audit is meant to catch.
- Forgetting `Get-LocalGroupMember`/`Add-LocalGroupMember`/`Remove-LocalGroupMember` require an elevated (Run as Administrator) session to succeed against most targets.
- Assuming a machine's local Administrators group only ever contains what GPO Restricted Groups pushed — ad-hoc manual additions bypass that control entirely and won't show up in a GPO review, only in a direct check like this one.

---

## 💡 Wisdom from Mímir

Every individually-added user in a local Administrators group is a decision someone made once, for a reason likely lost to time — auditing this group regularly, and converting ad-hoc grants to managed domain groups, is one of the highest-value, lowest-effort security habits in Windows administration.

---

## 🔗 Related Notes

- [[User and Session Cmdlets]]
- [[Active Directory Cmdlets]]
- [[Checking a User's Current Logon Session]]
- [[PowerShell Security Fundamentals]]
