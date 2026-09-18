---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Security+
difficulty: Intermediate
tags:
  - security-plus
  - security-operations
  - identity-access-management
aliases:
  - IAM Implementation Choice
publish: true
permalink: comptia-security-sy0-701/implementing-identity-and-access-management
---

# <span class="rune">ᚲ</span> Implementing Identity and Access Management

> *"Who decides access" is the single question that sorts every access control model on this exam.*

---

## 🎯 Problem

A scenario describes an access control need. Choose the correct model, protocol, or IAM practice.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "the resource owner decides who else can access it" | Discretionary access control (DAC) |
| "access is dictated by a classification system, not the owner" | Mandatory access control (MAC) |
| "access is based purely on job title/function" | Role-based access control (RBAC) |
| "access depends on a defined condition like time-of-day" | Rule-based access control |
| "access combines department, device compliance, and location together" | Attribute-based access control (ABAC) |
| "one login grants access to multiple unrelated systems" | SSO |
| "identity is trusted across two different organizations" | Federation |
| "elevated access is granted only for the duration of a specific task" | Just-in-time permissions |
| "credentials expire automatically after a short period rather than persisting" | Ephemeral credentials |
| "privileged credentials are stored and checked out as needed rather than memorized" | Password vaulting |
| "authentication combines a password and a hardware key" | MFA (knowledge + possession factors) |

---

## 🧠 Why This Pattern Works

Access control models differ by *who or what makes the access decision*: the owner (DAC), a system-enforced classification (MAC), a role (RBAC), a condition (rule-based), or a combination of attributes (ABAC). Privileged access management concepts (just-in-time, ephemeral credentials, vaulting) all share the same underlying goal — minimizing how long and how broadly elevated access exists, reducing the window an attacker could exploit it.

---

## 🚨 Common Mistakes

- Confusing RBAC (role alone decides) with ABAC (multiple attributes combine) — RBAC is a special case, ABAC is more flexible/granular
- Treating SSO and federation as identical — SSO is one login for multiple systems typically within one organization's control; federation extends trust *across* organizational boundaries
- Assuming MFA is satisfied by two examples from the same factor category (e.g., password + PIN, both "something you know")
- Leaving privileged access standing by default rather than applying just-in-time or ephemeral credential practices

---

## 💡 Wisdom from Mímir

Ask "who or what makes this access decision — a person, a policy, a role, or a combination of attributes?" That answer names the access control model directly.

---

## 🔗 Related Notes

- [[Enterprise Security Capabilities and Identity Access Management]] — full concept writeup
- [[Security+ Codex]]
