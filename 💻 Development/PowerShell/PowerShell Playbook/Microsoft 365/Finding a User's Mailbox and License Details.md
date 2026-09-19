---
type: powershell-pattern
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Intermediate
tags:
  - microsoft-365
  - mailbox
  - licensing
aliases:
  - Get-Mailbox
  - Get-MgUserLicenseDetail
publish: true
permalink: powershell/finding-a-users-mailbox-and-license-details
---

# <span class="rune">ᚲ</span> Finding a User's Mailbox and License Details

> *"This feature is missing" and "this mailbox is misconfigured" look identical to a user — but one lives in a license SKU and the other lives in Exchange, and confusing the two wastes the first ten minutes of nearly every ticket.*

---

## 🎯 Problem

A user reports a missing feature (no Teams access, can't use a specific Outlook capability) or a mailbox problem (can't receive mail, mailbox seems full). Determine whether the cause is licensing or mailbox configuration — two entirely separate systems that produce very similar-looking symptoms.

More generally, this pattern answers:
> What licenses does this user have assigned, and what does their actual mailbox configuration look like?

---

## 🤔 Mental Model

Think of a license as a ticket granting *entitlement* to a service, and the mailbox itself as the *actual provisioned resource* that entitlement enables. A user can hold a perfectly valid Exchange Online license (the ticket) while their mailbox is misconfigured, oversized, or has a forwarding rule silently redirecting mail (the actual resource behaving unexpectedly) — checking only one side of this pair, as introduced generally in [[Microsoft 365 and Entra ID Cmdlets]], leaves half the picture invisible.

---

## 🧠 Why This Pattern Works

`Get-MgUserLicenseDetail` queries Entra ID for exactly which license SKUs are assigned to a user — confirming *entitlement*. `Get-Mailbox`/`Get-MailboxStatistics` (via a separate Exchange Online connection) query the actual provisioned mailbox — confirming *configuration and current state*. Because these live in genuinely different systems, a complete investigation checks both independently rather than assuming one implies the other is also fine.

---

## 💻 PowerShell Solution

```powershell
# License entitlement check (requires Connect-MgGraph)
Get-MgUserLicenseDetail -UserId 'jdoe@contoso.com' | Select-Object SkuPartNumber

# Basic user profile/account state
Get-MgUser -UserId 'jdoe@contoso.com' -Property DisplayName, AccountEnabled, UserPrincipalName

# Mailbox configuration and current state (requires Connect-ExchangeOnline)
Get-Mailbox -Identity 'jdoe@contoso.com' |
    Select-Object DisplayName, RecipientTypeDetails, ForwardingSmtpAddress, ProhibitSendQuota

# Mailbox size/usage statistics
Get-MailboxStatistics -Identity 'jdoe@contoso.com' |
    Select-Object DisplayName, TotalItemSize, ItemCount
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Confirm license entitlement first

```powershell
Get-MgUserLicenseDetail -UserId 'jdoe@contoso.com'
```

Answers "does this user actually have a license that includes the feature/service in question" — a missing or wrong SKU here explains a feature-access complaint without needing to look at the mailbox at all.

### Step 2 — Check the mailbox's own configuration

```powershell
Get-Mailbox -Identity 'jdoe@contoso.com' | Select-Object ForwardingSmtpAddress, ProhibitSendQuota
```

`ForwardingSmtpAddress` populated unexpectedly is a very common, easily-missed cause of "my emails are disappearing" complaints — a silent forwarding rule, sometimes set up maliciously, sometimes leftover from a legitimate but forgotten configuration.

### Step 3 — Check actual mailbox size against its quota

```powershell
Get-MailboxStatistics -Identity 'jdoe@contoso.com' | Select-Object TotalItemSize, ItemCount
```

Directly answers a "mailbox full" complaint by comparing actual usage against the `ProhibitSendQuota` retrieved in the previous step.

---

## 🚀 Common Use Cases

- Diagnosing a "missing feature" complaint by confirming licensing before assuming a configuration or bug issue
- Investigating a "mailbox full" or "can't receive email" ticket with actual size and quota data
- Checking for unexpected mail forwarding rules as part of a security investigation
- Confirming a newly onboarded user's license and mailbox were both provisioned correctly

---

## ⚖️ Alternatives

### Checking all forwarding rules across the tenant (broader security sweep)

```powershell
Get-Mailbox -ResultSize Unlimited | Where-Object ForwardingSmtpAddress -ne $null |
    Select-Object DisplayName, ForwardingSmtpAddress
```

Useful for a security audit looking for any unexpected forwarding configured tenant-wide, not just for one specific user's ticket.

### Checking group-based license assignment specifically

```powershell
Get-MgUserMemberOf -UserId 'jdoe@contoso.com' |
    Where-Object { $_.AdditionalProperties.'@odata.type' -eq '#microsoft.graph.group' }
```

In tenants using group-based licensing, a user's actual license comes from group membership rather than direct assignment — checking direct `Get-MgUserLicenseDetail` alone can miss why a license is (or isn't) actually applied if the real control point is group membership.

---

## ⚠️ Common Mistakes

- Investigating a mailbox configuration issue without first confirming licensing, when the actual cause was a missing or wrong SKU the whole time.
- Not checking `ForwardingSmtpAddress` during a "missing emails" investigation — a silent forwarding rule is one of the most common and easily overlooked causes, and can also be a sign of account compromise worth investigating further.
- Assuming `Get-MgUserLicenseDetail` returning a license means every feature within that license's bundle is automatically enabled for the user — some services within a license SKU can still be individually disabled at the tenant or user level.
- Forgetting `Connect-ExchangeOnline` is a separate connection from `Connect-MgGraph` — attempting `Get-Mailbox` without it fails with a clear but sometimes-overlooked "not connected" error.

---

## 💡 Wisdom from Mímir

Check licensing and mailbox configuration as two independent, parallel investigations — never assume a valid license implies a correctly configured mailbox, or that a working mailbox implies correct licensing. They're two different systems, and a surprising number of tickets are actually caused by only one of the two being wrong.

---

## 🔗 Related Notes

- [[Microsoft 365 and Entra ID Cmdlets]]
- [[Connecting to Microsoft Graph or Exchange Online]]
- [[Checking a User's Group and License Assignments in Entra ID]]
- [[Resetting an Entra ID User's Password]]
