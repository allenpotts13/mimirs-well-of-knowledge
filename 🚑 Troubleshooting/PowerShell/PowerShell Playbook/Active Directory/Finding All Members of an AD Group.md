---
type: powershell-pattern
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Beginner
tags:
  - active-directory
  - group-membership
  - auditing
aliases:
  - Get-ADGroupMember
  - AD Group Membership
publish: true
permalink: powershell/finding-all-members-of-an-ad-group
---

# <span class="rune">ᚲ</span> Finding All Members of an AD Group

> *A group's membership list looks flat until a nested group is involved — and in most real directories, at least one is. `-Recursive` is the difference between an audit that's actually complete and one that only looks complete.*

---

## 🎯 Problem

Someone needs a definitive list of everyone who has access granted through a specific AD group — for an access review, an audit, or to confirm whether a specific user is actually a member.

More generally, this pattern answers:
> Who is really in this group, including members added indirectly through nested group membership?

---

## 🤔 Mental Model

Picture a group as a folder that can contain either files (users) or other folders (nested groups). Looking only at the top level of that folder tells you what's *directly* inside it — but anyone inside a nested folder still has access through the parent, exactly as if they'd been added directly. `Get-ADGroupMember -Recursive` is the difference between listing a folder's immediate contents and listing every file anywhere inside it, no matter how many folders deep.

---

## 🧠 Why This Pattern Works

`Get-ADGroupMember` without `-Recursive` returns only the group's direct members — which, in any directory with nested security groups (a very common design pattern, e.g., a department group added *into* a broader access group), understates who actually has the access that group grants. `-Recursive` walks the entire membership chain and expands every nested group down to its individual user accounts, which is what most real access-review questions actually need answered.

---

## 💻 PowerShell Solution

```powershell
# Direct members only
Get-ADGroupMember -Identity 'Help Desk' | Select-Object Name, SamAccountName, objectClass

# ALL members, including through nested groups
Get-ADGroupMember -Identity 'Help Desk' -Recursive |
    Select-Object Name, SamAccountName |
    Sort-Object Name

# Checking whether one specific user is a member (direct or nested)
$isMember = (Get-ADGroupMember -Identity 'Help Desk' -Recursive).SamAccountName -contains 'jdoe'
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — List direct members

```powershell
Get-ADGroupMember -Identity 'Help Desk'
```

Returns only what's directly assigned to the group — including nested groups themselves as single entries, not their individual members.

### Step 2 — Expand nested membership

```powershell
Get-ADGroupMember -Identity 'Help Desk' -Recursive
```

Walks through any nested groups and returns only the underlying user (and computer) accounts — the true, complete membership.

### Step 3 — Check for a specific user

```powershell
(Get-ADGroupMember -Identity 'Help Desk' -Recursive).SamAccountName -contains 'jdoe'
```

Pulls the full recursive list once, then tests membership against it — more efficient than querying per-user if checking several names against the same group.

---

## 🚀 Common Use Cases

- Access review/audit reporting who effectively has permissions granted through a specific group
- Confirming whether a user reporting "access denied" is actually a member of the group that should grant them access
- Exporting group membership to CSV for a compliance or security audit
- Investigating unexpected access — tracing how a user ended up with a permission through a nested group they weren't directly added to

---

## ⚖️ Alternatives

### The reverse direction — what groups is a specific user a member of

```powershell
Get-ADUser -Identity 'jdoe' -Properties MemberOf |
    Select-Object -ExpandProperty MemberOf
```

Answers the mirror-image question: instead of "who's in this group," it's "what groups is this user in" — `MemberOf` only shows *direct* memberships, so a user's effective nested-group access isn't fully visible this way without also checking each returned group's own parent memberships.

### Exporting recursive membership to CSV for an audit

```powershell
Get-ADGroupMember -Identity 'Help Desk' -Recursive |
    Get-ADUser -Properties Department, Title |
    Select-Object Name, SamAccountName, Department, Title |
    Export-Csv -Path .\help-desk-members.csv -NoTypeInformation
```

Piping the recursive results into `Get-ADUser` enriches each member with additional properties beyond what `Get-ADGroupMember` itself returns.

---

## ⚠️ Common Mistakes

- Running `Get-ADGroupMember` without `-Recursive` for an access audit and reporting an incomplete list, missing anyone who has access purely through a nested group.
- Assuming `-Recursive` also returns disabled accounts consistently — a disabled user account that's still a group member is still returned, but audits often need to separately check `Enabled` status to interpret the result correctly.
- Piping `Get-ADGroupMember -Recursive` results directly into `Get-ADUser -Properties *` for a very large group, which can be slow — request only the specific properties actually needed.
- Confusing group **membership** (`Get-ADGroupMember`) with group **management** rights (who can *modify* the group) — these are entirely separate AD concepts (`ManagedBy` and delegated permissions, not membership).

---

## 💡 Wisdom from Mímir

Never trust a non-recursive membership list for an access review — nested groups are common enough in real directories that skipping `-Recursive` doesn't just risk an edge case, it risks systematically understating who actually has access every single time.

---

## 🔗 Related Notes

- [[Active Directory Cmdlets]]
- [[Managing Local Administrator Group Membership]]
- [[Finding Stale or Inactive AD Computer Accounts]]
- [[Checking a User's Group and License Assignments in Entra ID]]
