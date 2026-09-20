---
type: concept
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Intermediate
tags:
  - powershell-domain-reference
  - microsoft-365
  - entra-id
aliases:
  - Microsoft Graph PowerShell
  - Connect-MgGraph
publish: true
permalink: powershell/microsoft-365-and-entra-id-cmdlets
---

# <span class="rune">ᛟ</span> Microsoft 365 and Entra ID Cmdlets

> *On-premises AD has one module and one mental model. Cloud identity splits the same troubleshooting questions across Microsoft Graph and Exchange Online — different modules, different connection steps, same underlying goal.*

---

## 🎯 Purpose

Managing Microsoft 365 and Entra ID (formerly Azure AD) from PowerShell means working primarily through the **Microsoft.Graph** module family (the modern, actively developed interface to Entra ID, licensing, and most Microsoft 365 services) and **ExchangeOnlineManagement** (specifically for mailbox and Exchange Online configuration). Both replaced older, now-deprecated modules (`AzureAD`, `MSOnline`), so any older documentation or scripts referencing those need translation to their Graph equivalents.

---

## 🧠 Key Ideas

- `Connect-MgGraph -Scopes '<permission scopes>'` establishes an authenticated session — unlike on-prem AD, cloud identity work requires explicit, scoped permission consent rather than relying purely on the calling user's existing rights.
- `Get-MgUser`, `Get-MgGroup` are the Graph equivalents of `Get-ADUser`/`Get-ADGroup`, but property names and available properties differ meaningfully from their on-prem AD counterparts.
- `Connect-ExchangeOnline` is a **separate** connection required specifically for mailbox-related cmdlets (`Get-Mailbox`, `Get-MailboxStatistics`) — Graph alone doesn't cover deep Exchange Online mailbox configuration.
- License assignment in Entra ID is managed through SKU-based cmdlets (`Get-MgUserLicenseDetail`, `Set-MgUserLicense`) — a user's license state is a distinct query from their basic account properties.
- The older `AzureAD` and `MSOnline` modules are **deprecated and being retired** by Microsoft — any script or reference still using `Get-AzureADUser` or `Get-MsolUser` should be treated as legacy and migrated to Microsoft Graph equivalents.

---

## ⚙️ How It Works

Microsoft Graph is a REST API, and the `Microsoft.Graph` PowerShell module is a wrapper around it — meaning every cmdlet call is really an authenticated HTTPS request to Microsoft's cloud endpoints, unlike on-prem AD's direct LDAP connection to a domain controller. This is why `Connect-MgGraph` requires explicit permission **scopes** (like `User.Read.All` or `Group.ReadWrite.All`) to be requested and consented to up front — the Graph API enforces least-privilege access at the API layer itself, rather than simply inheriting whatever rights the calling account happens to already have on-premises.

Exchange Online's mailbox-level detail lives outside Graph's coverage for historical reasons, which is why a genuinely complete Microsoft 365 identity/mailbox investigation typically requires both `Connect-MgGraph` (for the user/group/license side) and `Connect-ExchangeOnline` (for the mailbox side) in the same session.

```text
Connect-MgGraph -Scopes 'User.Read.All','Group.Read.All'
        ↓
  Get-MgUser -UserId 'user@contoso.com'    → identity/profile/license data
        
Connect-ExchangeOnline
        ↓
  Get-Mailbox -Identity 'user@contoso.com' → mailbox-specific configuration
```

---

## 💻 Examples

```powershell
# Connect to Microsoft Graph with the scopes needed for the task
Connect-MgGraph -Scopes 'User.Read.All', 'Group.Read.All'

# Basic user lookup
Get-MgUser -UserId 'jdoe@contoso.com' -Property DisplayName, UserPrincipalName, AccountEnabled

# Group membership
Get-MgUserMemberOf -UserId 'jdoe@contoso.com'

# License details for a user
Get-MgUserLicenseDetail -UserId 'jdoe@contoso.com'

# Separate connection required for mailbox-level work
Connect-ExchangeOnline
Get-Mailbox -Identity 'jdoe@contoso.com'
Get-MailboxStatistics -Identity 'jdoe@contoso.com' | Select-Object DisplayName, TotalItemSize

# Disconnecting cleanly when finished
Disconnect-MgGraph
Disconnect-ExchangeOnline -Confirm:$false
```

---

## 🚀 Real World Applications

- Looking up a user's Entra ID account status and group memberships during a "can't access this app" ticket
- Checking a user's assigned licenses to confirm whether a missing feature is a licensing issue rather than a bug
- Pulling mailbox size/statistics via Exchange Online to troubleshoot a "mailbox full" or sync issue
- Auditing sign-in or account state across the tenant as part of a security or offboarding review

---

## ⚖️ Advantages

- Microsoft Graph is actively developed and continuously extended with new Microsoft 365 capabilities, unlike the deprecated `AzureAD`/`MSOnline` modules.
- Scoped permission consent (`-Scopes`) means a script only requests exactly the access it needs, supporting least-privilege practices.
- The same Graph module covers users, groups, licensing, and much of Entra ID in one consistent cmdlet family.
- Works identically whether run interactively or via app-only (service principal/certificate) authentication for unattended automation.

---

## ⚠️ Limitations

- Requires internet connectivity and appropriate Entra ID role/permission consent — unlike on-prem AD, there's no "just works because you're on the domain" fallback.
- Mailbox-specific detail requires a separate `Connect-ExchangeOnline` session — Graph's coverage of Exchange Online configuration remains incomplete for some scenarios.
- Cmdlet and property names don't map one-to-one with on-prem AD equivalents, requiring some relearning even for an experienced AD administrator.
- Legacy scripts referencing `AzureAD`/`MSOnline` module cmdlets will increasingly break as Microsoft continues retiring those modules — migration isn't optional long-term.

---

## 🚨 Common Mistakes

- Running `Connect-MgGraph` with overly broad scopes (`.ReadWrite.All` everywhere) instead of the minimum needed for the task, undermining least-privilege intent.
- Assuming `Get-MgUser` alone covers mailbox questions, and not realizing `Connect-ExchangeOnline`/`Get-Mailbox` is a separate, required connection for that data.
- Continuing to write new scripts against the deprecated `AzureAD`/`MSOnline` modules instead of Microsoft Graph, creating technical debt that will need migration regardless.
- Forgetting to `Disconnect-MgGraph`/`Disconnect-ExchangeOnline` in long-running or scheduled scripts, leaving stale authenticated sessions or token consumption behind.

---

## 📖 Further Reading

- `Get-Help Connect-MgGraph -Full`
- Microsoft Learn: "Microsoft Graph PowerShell SDK overview"
- Microsoft Learn: "Connect to Exchange Online PowerShell"

---

## 💡 Wisdom from Mímir

Treat any script or guide referencing `Get-AzureADUser` or `Get-MsolUser` as outdated the moment you see it — translate the intent to its Microsoft Graph equivalent rather than trying to keep the deprecated module working.

---

## 🔗 Related Notes

- [[Active Directory Cmdlets]]
- [[Connecting to Microsoft Graph or Exchange Online]]
- [[Modules]]
- [[PowerShell Codex]]
