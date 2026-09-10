---
type: exam-scenario
status: active
created: 2026-09-10
updated: 2026-09-10
technology: Azure
difficulty: Beginner
tags:
  - az-900
  - identity
  - entra-id
aliases:
  - Identity Tool Choice
---

# 🧩 Choosing an Identity and Access Tool

> *Entra ID is the platform; MFA, Conditional Access, and External Identities are the features layered on top of it for specific jobs.*

---

## 🎯 Problem

A scenario describes an identity/access requirement (verify a user is who they claim, restrict access based on context, let outside users in). Pick the matching Entra ID feature.

---

## 🤔 Mental Model

| Scenario signal | Tool |
|---|---|
| "Require a second verification step beyond a password" | Multi-Factor Authentication (MFA) |
| "Block sign-in from outside the corporate network, or from risky locations" | Conditional Access |
| "Let a partner company's users log in to access a shared app" | External Identities (B2B) |
| "Let customers create their own accounts to use a customer-facing app" | External Identities (B2C) |
| "One login grants access to multiple applications" | Single sign-on (SSO) |
| "Central directory of users and groups for the whole org" | Microsoft Entra ID itself |

---

## 🧠 Why This Pattern Works

Entra ID is the foundational identity directory; everything else in this list is a policy or feature that consumes that directory to make a specific decision. MFA decides *how* you prove who you are. Conditional Access decides *whether* you're allowed to sign in at all, given context. External Identities decides *who's allowed in the directory in the first place* if they're not an employee.

---

## 🚨 Common Mistakes

- Answering "MFA" for a scenario that's actually about *conditions* (location, device compliance) rather than a second verification factor — that's Conditional Access
- Confusing B2B (inviting external partners into your directory) with B2C (building a directory of external customers for your own app)

---

## 💡 Wisdom from Mímir

MFA asks "how do you prove it's you." Conditional Access asks "under what circumstances do we even let you try." Keep those two questions separate and most of these questions answer themselves.

---

## 🔗 Related Notes

- [[Identity, Access, and Security]] — full concept writeup
- [[AZ-900 Codex]]
