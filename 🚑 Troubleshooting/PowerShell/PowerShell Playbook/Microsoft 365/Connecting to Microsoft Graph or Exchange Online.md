---
type: powershell-pattern
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Beginner
tags:
  - microsoft-365
  - microsoft-graph
  - exchange-online
aliases:
  - Connect-MgGraph
  - Connect-ExchangeOnline
publish: true
permalink: powershell/connecting-to-microsoft-graph-or-exchange-online
---

# <span class="rune">ᚲ</span> Connecting to Microsoft Graph or Exchange Online

> *Every Microsoft 365 troubleshooting session starts the same way — not with a query, but with proving you're allowed to ask the question at all.*

---

## 🎯 Problem

Before running any Entra ID or Exchange Online cmdlet, a properly-scoped, authenticated session needs to be established — and knowing exactly which permission scopes a given task actually needs, rather than requesting broad access out of convenience.

More generally, this pattern answers:
> How do I connect to Microsoft Graph and/or Exchange Online with the minimum permission scopes needed for a specific troubleshooting task?

---

## 🤔 Mental Model

Treat each `Connect-MgGraph -Scopes` call like requesting a specific, named key rather than the master key to the whole building — asking only for `User.Read.All` when the task is "look up a user's profile" is both safer and more transparent about intent than defaulting to broad `.ReadWrite.All` scopes out of habit. As covered generally in [[Microsoft 365 and Entra ID Cmdlets]], this scoped-consent model is a deliberate design choice distinguishing cloud identity management from the more implicitly-trusted on-premises AD model.

---

## 🧠 Why This Pattern Works

`Connect-MgGraph` authenticates against Microsoft Entra ID and establishes a session carrying exactly the permission scopes requested — every subsequent cmdlet call in that session is constrained to what those scopes allow, and a cmdlet requiring a scope that wasn't requested will fail with a clear permissions error rather than silently succeeding. `Connect-ExchangeOnline` is a separate, independent authentication specifically for the Exchange Online management endpoints, needed because — as explained in [[Microsoft 365 and Entra ID Cmdlets]] — Graph's coverage of deep mailbox configuration remains incomplete.

---

## 💻 PowerShell Solution

```powershell
# Install once, if not already present
Install-Module Microsoft.Graph -Scope CurrentUser
Install-Module ExchangeOnlineManagement -Scope CurrentUser

# Connect to Graph with only the scopes needed for a user/license lookup task
Connect-MgGraph -Scopes 'User.Read.All', 'Group.Read.All'

# Connect to Exchange Online separately, for mailbox-specific work
Connect-ExchangeOnline

# Confirm what's currently connected and with which scopes
Get-MgContext | Select-Object Scopes, Account
Get-ConnectionInformation | Select-Object UserPrincipalName, State

# Disconnect cleanly when finished
Disconnect-MgGraph
Disconnect-ExchangeOnline -Confirm:$false
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Install the required modules once

```powershell
Install-Module Microsoft.Graph -Scope CurrentUser
Install-Module ExchangeOnlineManagement -Scope CurrentUser
```

`-Scope CurrentUser` avoids needing an elevated session just to install the modules — appropriate for most individual troubleshooting use.

### Step 2 — Connect with the minimum scopes the task actually needs

```powershell
Connect-MgGraph -Scopes 'User.Read.All', 'Group.Read.All'
```

Requesting only read scopes for a read-only investigation, rather than defaulting to write scopes "just in case," keeps the session's actual capability matched to its real intent.

### Step 3 — Connect to Exchange Online separately, only if mailbox-level detail is needed

```powershell
Connect-ExchangeOnline
```

A distinct authentication step — not covered by the Graph connection above — required specifically for `Get-Mailbox`/`Get-MailboxStatistics` and similar Exchange-specific cmdlets.

---

## 🚀 Common Use Cases

- The mandatory first step before any Entra ID user/group/license investigation
- Establishing a session for a scripted, unattended report needing only read access
- Combining both connections in one troubleshooting session when a ticket spans both identity and mailbox questions
- Confirming exactly what scopes an existing session actually has before assuming a cmdlet will work

---

## ⚖️ Alternatives

### App-only (certificate-based) authentication for unattended automation

```powershell
Connect-MgGraph -ClientId $appId -TenantId $tenantId -CertificateThumbprint $thumbprint
```

Used for scheduled, non-interactive scripts where no human is present to complete an interactive sign-in — requires an app registration with the appropriate application permissions pre-configured in Entra ID.

### Checking currently active scopes mid-session

```powershell
(Get-MgContext).Scopes
```

Useful when picking up an already-open session and needing to confirm whether it has sufficient scope for a new task, before attempting a cmdlet that might otherwise fail with a permissions error.

---

## ⚠️ Common Mistakes

- Requesting broad `.ReadWrite.All` scopes by default instead of the specific minimum scopes a task actually requires, undermining the least-privilege intent the scoped-consent model is designed for.
- Assuming a single `Connect-MgGraph` call also covers Exchange Online mailbox cmdlets — it doesn't; `Connect-ExchangeOnline` is a separate, required connection for that data.
- Not disconnecting sessions (`Disconnect-MgGraph`/`Disconnect-ExchangeOnline`) in long-running or scheduled scripts, leaving stale authenticated sessions or unnecessary token consumption behind.
- Forgetting that interactive `Connect-MgGraph` requires a signed-in user to complete consent — unattended scripts need the app-only certificate-based flow instead, which requires separate Entra ID app registration setup ahead of time.

---

## 📖 Further Reading

- Microsoft Learn: "Microsoft Graph PowerShell SDK overview" (permission scopes reference)
- Microsoft Learn: "Connect to Exchange Online PowerShell"

---

## 💡 Wisdom from Mímir

Before running `Connect-MgGraph`, know exactly which scope the task needs and request only that — treating scope selection as a deliberate step, not an afterthought, is what actually delivers on the least-privilege model Microsoft Graph was built around.

---

## 🔗 Related Notes

- [[Microsoft 365 and Entra ID Cmdlets]]
- [[Finding a User's Mailbox and License Details]]
- [[Modules]]
- [[PowerShell Security Fundamentals]]
