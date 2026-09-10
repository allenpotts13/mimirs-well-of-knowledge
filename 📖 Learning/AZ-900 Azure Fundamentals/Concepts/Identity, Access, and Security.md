---
type: concept
status: active
created: 2026-09-10
updated: 2026-09-10
technology: Azure
difficulty: Beginner
tags:
  - az-900
  - identity
  - security
  - entra-id
  - rbac
aliases:
  - Microsoft Entra ID
  - Azure RBAC
  - Zero Trust
---

# 📚 Identity, Access, and Security

> *Knowing who someone is (authentication), deciding what they're allowed to touch (authorization), and assuming every request is hostile until proven otherwise (Zero Trust).*

---

## 🎯 Purpose

Technically part of the "Azure architecture and services" domain, but dense enough to earn its own note. Covers identity services, access control models, and Microsoft's security philosophy/tooling.

---

## 🧠 Key Ideas

- **Authentication** (who are you?) is a different concept from **authorization** (what can you do?)
- Microsoft Entra ID (formerly Azure Active Directory) is Azure's identity platform
- Azure RBAC assigns permissions via role + scope, not per-resource custom rules
- Zero Trust and defense in depth are security *philosophies*, not products
- Microsoft Defender for Cloud is the tool that operationalizes these philosophies

---

## ⚙️ How It Works

### Authentication vs authorization

| | Authentication (AuthN) | Authorization (AuthZ) |
|---|---|---|
| Question | Who are you? | What are you allowed to do? |
| Azure tool | Microsoft Entra ID, MFA | Azure RBAC |

### Microsoft Entra ID (identity platform)

- Cloud-based identity and access management service
- Manages users, groups, and authentication for Azure, Microsoft 365, and third-party SaaS apps
- **Multi-factor authentication (MFA)** — requires 2+ verification methods (something you know/have/are)
- **Conditional Access** — policies that grant/block access based on conditions (location, device, risk level)
- **External Identities** — lets you collaborate with users outside your organization (B2B) or expose apps to external customers (B2C)
- **Single sign-on (SSO)** — one login grants access to multiple applications

### Azure RBAC (Role-Based Access Control)

- Assigns access by combining: **security principal** (who) + **role definition** (what they can do) + **scope** (where it applies)
- Scope can be a management group, subscription, resource group, or individual resource — permissions inherit downward
- Built-in roles: **Owner** (full access + can assign roles), **Contributor** (full access, can't assign roles), **Reader** (view only)

```
Management Group
    └── Subscription
            └── Resource Group
                    └── Resource
```
A role assigned at any level flows down to everything beneath it.

### Resource locks

- Prevent accidental deletion or modification, independent of RBAC permissions
- **CanNotDelete** — resource can be read/modified but not deleted
- **ReadOnly** — resource can't be modified or deleted, only read
- Locks apply regardless of a user's RBAC role — even Owners are blocked by a lock

### Defense in depth

Layered security model — no single layer is trusted alone:

```
Data → Application → Compute → Network → Perimeter → Identity & Access → Physical Security
```
Each layer adds a barrier so a breach at one layer doesn't compromise everything.

### Zero Trust model

Core principle: **"never trust, always verify."** Every request is authenticated, authorized, and encrypted as if it originates from an open, untrusted network — regardless of where it comes from.

Three guiding principles: verify explicitly, use least-privilege access, assume breach.

### Microsoft Defender for Cloud

Unified security management tool that assesses resources against security best practices, provides a **secure score**, and offers threat protection recommendations across hybrid/multi-cloud workloads.

---

## 💻 Examples

```text
"A user proves their identity with a password plus a code from an app."
→ Multi-factor authentication (MFA)

"Grant a team Contributor access to every resource in a subscription in
one assignment."
→ Azure RBAC, assigned at the subscription scope

"Prevent a critical production storage account from being deleted, even
by an Owner."
→ Resource lock, CanNotDelete

"Every request is verified regardless of whether it originates inside
the corporate network."
→ Zero Trust
```

---

## 🚨 Common Mistakes

- Confusing authentication (identity proof) with authorization (permission check) — they're sequential, not the same step
- Assuming RBAC roles are assigned per-resource by default — they're assigned at a scope and inherit downward
- Thinking a resource lock is a permission — it overrides RBAC entirely, even for Owners

---

## 📖 Further Reading

- [Microsoft Learn: Describe Azure identity, access, and security](https://learn.microsoft.com/en-us/training/modules/describe-azure-identity-access-security/)

---

## 🔗 Related Notes

- [[Core Azure Architecture and Services]]
- [[Azure RBAC vs Azure Policy vs Resource Locks]] — drill note
- [[Choosing an Identity and Access Tool]] — drill note
- [[AZ-900 Codex]]
