---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Network+
difficulty: Intermediate
tags:
  - network-plus
  - network-security
aliases:
  - AAA Protocol Choice
publish: true
permalink: comptia-network-n10-009/choosing-the-right-authentication-method
---

# <span class="rune">ᚲ</span> Choosing the Right Authentication Method

> *RADIUS, TACACS+, LDAP, SAML — four acronyms that all mean "prove who you are," each in a different context.*

---

## 🎯 Problem

A scenario describes an authentication or directory requirement. Choose the correct protocol.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "centralized AAA for Wi-Fi or network device logins" | RADIUS |
| "centralized AAA with authentication, authorization, and accounting split into separate processes (Cisco-favored)" | TACACS+ |
| "look up user/group information in a corporate directory" | LDAP |
| "single sign-on between an identity provider and a web application" | SAML |
| "one login grants access across multiple systems generally" | SSO |
| "login requires more than one type of proof" | MFA |
| "access is restricted based on physical location" | Geofencing |
| "access is restricted to only what a role strictly needs" | Least privilege / RBAC |

---

## 🧠 Why This Pattern Works

RADIUS and TACACS+ both provide centralized AAA but differ in heritage and granularity — RADIUS is the broader, more common standard (Wi-Fi, VPN, network device auth), while TACACS+ separates authentication/authorization/accounting more distinctly and is favored for administrative device access in Cisco environments. LDAP is fundamentally a *directory lookup* protocol, not an authentication protocol itself, though it's commonly used to back authentication. SAML specifically enables web-based SSO by exchanging authentication assertions between an identity provider and a service provider.

---

## 🚨 Common Mistakes

- Treating RADIUS and TACACS+ as fully interchangeable — TACACS+'s separated AAA processes make it preferred for granular administrative command authorization
- Calling LDAP an authentication protocol on its own — it's a directory access protocol, often paired *with* an authentication mechanism
- Confusing SSO (the general concept of one login, many systems) with SAML (one specific standard that implements SSO for web applications)
- Assuming MFA and SSO conflict — they're complementary; SSO reduces password fatigue while MFA strengthens the single login that grants broad access

---

## 💡 Wisdom from Mímir

If the scenario is about network device or Wi-Fi login, think RADIUS or TACACS+. If it's about a directory lookup, think LDAP. If it's about web app single sign-on, think SAML.

---

## 🔗 Related Notes

- [[Network Security Fundamentals and Compliance]] — full concept writeup
- [[Network+ Codex]]
