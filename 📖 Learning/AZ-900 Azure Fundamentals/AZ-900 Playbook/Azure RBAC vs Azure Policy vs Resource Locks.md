---
type: exam-scenario
status: published
created: 2026-09-10
updated: 2026-09-10
technology: Azure
difficulty: Beginner
tags:
  - az-900
  - governance
  - rbac
  - policy
aliases:
  - Governance Tool Choice
publish: true
permalink: az-900-azure-fundamentals/azure-rbac-vs-azure-policy-vs-resource-locks
---

# <span class="rune">ᚲ</span> Azure RBAC vs Azure Policy vs Resource Locks

> *Three governance tools that all sound like "control what happens in Azure," but each controls a different question.*

---

## 🎯 Problem

A scenario describes wanting to restrict or control something in Azure. Decide whether the tool is RBAC, Policy, or a resource lock.

---

## 🤔 Mental Model

| Tool | Question it answers |
|---|---|
| **Azure RBAC** | *Who* can do *what*? (permissions) |
| **Azure Policy** | *What* is allowed to exist / be configured? (compliance rules) |
| **Resource locks** | Can this *specific resource* be deleted or changed, regardless of permissions? |

---

## 🔑 Decision Table

| Scenario signal | Tool |
|---|---|
| "Grant the dev team Contributor access to a resource group" | RBAC |
| "Prevent anyone from deploying VMs outside approved regions" | Policy |
| "Stop a production database from being deleted, even by an Owner" | Resource lock (CanNotDelete) |
| "Audit existing resources for compliance with naming standards" | Policy |
| "Allow viewing but not editing a resource, for anyone" | Resource lock (ReadOnly) — note this affects *everyone*, unlike RBAC which is scoped per-principal |

---

## 🧠 Why This Pattern Works

RBAC is about identity — it answers questions about a person or group. Policy is about resource state — it evaluates configuration against rules, independent of who's making the change. Resource locks are the blunt-force override — they ignore identity and permissions entirely and just block an action outright. All three can be layered together without conflicting, because they're answering different questions.

---

## 🚨 Common Mistakes

- Reaching for RBAC to enforce "only allow Standard_B1s VM sizes" — that's a resource configuration rule, which is Policy's job, not a permissions question
- Assuming an Owner can override a resource lock — locks apply to everyone, Owner included
- Using a resource lock where Policy would be more scalable — locks are per-resource and manual; Policy can enforce rules automatically across many resources at once

---

## 💡 Wisdom from Mímir

If the question is about a *person's* permissions, it's RBAC. If it's about a *resource's* allowed configuration, it's Policy. If it's about *preventing an action outright regardless of who's asking*, it's a lock.

---

## 🔗 Related Notes

- [[Identity, Access, and Security]]
- [[Azure Management and Governance]] — full concept writeup
- [[AZ-900 Codex]]
