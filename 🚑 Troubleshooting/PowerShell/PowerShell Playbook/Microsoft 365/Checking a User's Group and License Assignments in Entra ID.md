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
  - group-based-licensing
aliases:
  - Get-MgUserMemberOf
  - Group-Based Licensing PowerShell
publish: true
permalink: powershell/checking-a-users-group-and-license-assignments-in-entra-id
---

# <span class="rune">ᚲ</span> Checking a User's Group and License Assignments in Entra ID

> *A license assigned directly to a user and a license inherited through a group look identical in the end result — but only one of them explains itself when you go looking for why it's there.*

---

## 🎯 Problem

A user has an unexpected license (or is missing an expected one), and the cause needs to be traced — was it assigned directly, or does it come from group membership, and if the latter, which group?

More generally, this pattern answers:
> What groups is this user a member of, and which of those groups (or direct assignments) are actually driving their license entitlements?

---

## 🤔 Mental Model

Think of group-based licensing like an inherited membership perk — a magazine subscription that came bundled with a club membership, not something purchased individually. Cancelling the individual subscription does nothing if the club membership itself still grants it; the fix has to happen at whichever layer is actually the source. Tracing a license back to its real origin (direct assignment vs. a specific group) is exactly this kind of "where does this actually come from" investigation.

---

## 🧠 Why This Pattern Works

`Get-MgUserMemberOf` lists every group (and role) a user belongs to, while `Get-MgGroupLicenseDetail` (checked against each of those groups) reveals which of them have licenses configured for group-based assignment — cross-referencing the two identifies exactly which group, if any, is the actual source of a given license, distinct from a license assigned directly to the user which needs no such tracing at all.

---

## 💻 PowerShell Solution

```powershell
Connect-MgGraph -Scopes 'User.Read.All', 'Group.Read.All'

# All groups (and directory roles) the user belongs to
Get-MgUserMemberOf -UserId 'jdoe@contoso.com' |
    Select-Object Id, @{N='DisplayName';E={$_.AdditionalProperties.displayName}}

# The user's actual current license assignments
Get-MgUserLicenseDetail -UserId 'jdoe@contoso.com' | Select-Object SkuPartNumber

# Check each group the user belongs to for group-based license assignment
Get-MgUserMemberOf -UserId 'jdoe@contoso.com' | ForEach-Object {
    $groupId = $_.Id
    $licenses = Get-MgGroupLicenseDetail -GroupId $groupId -ErrorAction SilentlyContinue
    if ($licenses) {
        [pscustomobject]@{
            GroupName = $_.AdditionalProperties.displayName
            Licenses  = $licenses.SkuPartNumber -join ', '
        }
    }
}
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — List the user's group memberships

```powershell
Get-MgUserMemberOf -UserId 'jdoe@contoso.com'
```

Returns every group and directory role the user belongs to — the starting point for tracing any inherited entitlement.

### Step 2 — Confirm the user's actual current licenses

```powershell
Get-MgUserLicenseDetail -UserId 'jdoe@contoso.com'
```

Establishes what's actually assigned right now, regardless of where it came from — the fact being investigated.

### Step 3 — Cross-reference groups against license assignment

```powershell
Get-MgGroupLicenseDetail -GroupId $groupId
```

Checking each group the user belongs to for its own license configuration identifies exactly which membership, if any, is the actual source of a given entitlement — rather than assuming it must be a direct assignment.

---

## 🚀 Common Use Cases

- Tracing why a user has a license that wasn't directly assigned to them, back to the responsible group
- Investigating a missing license by confirming whether the expected group-based assignment actually applied
- Auditing access and entitlement inheritance as part of an offboarding or role-change process
- Confirming a group's license configuration is behaving as expected after adding or removing group members

---

## ⚖️ Alternatives

### Checking a specific group's full membership and its license configuration together

```powershell
Get-MgGroupMember -GroupId $groupId | Select-Object Id,
    @{N='DisplayName';E={$_.AdditionalProperties.displayName}}
Get-MgGroupLicenseDetail -GroupId $groupId
```

Useful when starting from "which users get this license" rather than "why does this user have this license" — the reverse direction of the same investigation.

### Removing a user from a licensing group as the actual remediation

```powershell
Remove-MgGroupMemberByRef -GroupId $groupId -DirectoryObjectId $userId
```

Once the responsible group is identified, this is typically the correct remediation — directly removing an inherited license from a user without addressing the group membership itself won't stick, since the group assignment will simply reapply it.

---

## ⚠️ Common Mistakes

- Attempting to remove a group-inherited license by editing the user's direct license assignment — group-based licensing reasserts itself automatically as long as the membership remains, making a direct removal ineffective.
- Not checking every group the user belongs to — a user can belong to several groups, only one of which actually has license assignment configured, and the responsible one isn't always the obviously-named one.
- Assuming `Get-MgUserLicenseDetail` distinguishes direct from group-based assignment in its own output — it doesn't; the cross-referencing against `Get-MgGroupLicenseDetail` is what's actually needed to make that distinction.
- Forgetting license processing for group-based assignment isn't always instantaneous — a recent group membership change may take some time to actually reflect in the user's license state.

---

## 💡 Wisdom from Mímir

Never try to fix a group-inherited license by editing the user directly — trace it back to the responsible group first. Anything done at the user level alone will simply be overwritten the next time Entra ID re-evaluates that group's license assignment.

---

## 🔗 Related Notes

- [[Microsoft 365 and Entra ID Cmdlets]]
- [[Finding a User's Mailbox and License Details]]
- [[Finding All Members of an AD Group]]
- [[Connecting to Microsoft Graph or Exchange Online]]
