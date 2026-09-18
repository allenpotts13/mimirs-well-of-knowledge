---
type: concept
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
  - Security+ Enterprise Capabilities
  - Security+ IAM
publish: true
permalink: comptia-security-sy0-701/enterprise-security-capabilities-and-identity-access-management
---

# <span class="rune">ᛟ</span> Enterprise Security Capabilities and Identity Access Management

> *This note has more acronyms per paragraph than almost anything else in the certification — and every one of them is separately testable.*

---

## 🎯 Purpose

Covers 4.5-4.6 of the Security Operations domain: modifying enterprise capabilities to enhance security, and implementing/maintaining identity and access management — both "given a scenario" objectives.

---

## 🧠 Key Ideas

- Enterprise security capabilities span **network-facing** tools (firewall, IDS/IPS, web filter, DNS filter, email security) and **endpoint/host-facing** tools (OS security via Group Policy/SELinux, FIM, DLP, NAC, EDR/XDR, user behavior analytics)
- Email security is largely about **anti-spoofing**: DMARC, DKIM, and SPF each verify a different aspect of message authenticity
- Access control models each answer "who decides access" differently: **discretionary** (owner decides), **mandatory** (system/policy decides), **role-based** (job function decides), **rule-based** (conditions decide), **attribute-based** (multiple attributes decide)
- **MFA factors** come from four categories: something you know, have, are, and *somewhere you are* — true MFA combines factors from different categories, not just multiple examples of one
- Privileged access management tools (just-in-time permissions, password vaulting, ephemeral credentials) all share one goal: minimize how long elevated access exists

---

## ⚙️ How It Works

### Network and endpoint security capabilities

| Category | Tools |
|---|---|
| Network | Firewall (rules, ACLs, screened subnets), IDS/IPS (trends, signatures), web filter (agent/proxy/URL/category/reputation-based), DNS filtering, email security |
| Endpoint | OS security (Group Policy, SELinux), secure protocol implementation, FIM, DLP, NAC, EDR/XDR, user behavior analytics |

**Email security (anti-spoofing trio):**

| Record | Verifies |
|---|---|
| SPF | Which servers are authorized to send mail for a domain |
| DKIM | Message integrity via a cryptographic signature |
| DMARC | Policy for what to do when SPF/DKIM checks fail, plus reporting |

### Identity and access management

| Access control model | Decided by |
|---|---|
| Discretionary (DAC) | The resource owner |
| Mandatory (MAC) | System-enforced policy/classification |
| Role-based (RBAC) | Job role |
| Rule-based | Defined conditions (e.g., time-of-day) |
| Attribute-based (ABAC) | Multiple attributes combined |

| IAM concept | Purpose |
|---|---|
| Provisioning/de-provisioning | Creating/removing account access at the right time |
| Identity proofing | Verifying a person is who they claim before granting identity |
| Federation | Trusting identity across organizational boundaries |
| SSO (LDAP, OAuth, SAML) | One login, many systems |
| Interoperability / Attestation | Systems working together; formally confirming access is still appropriate |

### MFA factors

| Factor | Example |
|---|---|
| Something you know | Password, PIN |
| Something you have | Hardware/soft token, security key |
| Something you are | Biometrics |
| Somewhere you are | Geolocation |

### Password concepts and privileged access

| Concept | Detail |
|---|---|
| Password best practices | Length, complexity, reuse restrictions, expiration, age minimums |
| Password managers / Passwordless | Reduce reuse risk / eliminate the password entirely |
| Privileged access management | Just-in-time permissions, password vaulting, ephemeral credentials — all minimize standing elevated access |

---

## 💻 Examples

```text
"An email fails SPF but the domain has no DMARC policy configured, so it
gets delivered anyway despite looking spoofed."
→ Missing DMARC enforcement policy

"Access is granted based on a combination of department, clearance level,
and device compliance state, evaluated together."
→ Attribute-based access control (ABAC)

"A user logs in with a password and a hardware security key."
→ True MFA — knowledge factor + possession factor

"An administrator's elevated access is granted for exactly 30 minutes to
complete one task, then automatically revoked."
→ Just-in-time permissions (privileged access management)

"A federated login lets an employee access a partner company's portal
using their home organization's credentials."
→ Federation
```

---

## 🚨 Common Mistakes

- Treating two knowledge-based checks (e.g., password + security question) as true MFA — both come from the same factor category
- Confusing SPF (authorized sending servers), DKIM (message signature), and DMARC (enforcement policy) as interchangeable — each verifies something different
- Assuming RBAC and ABAC are the same because both are "based on attributes of the user" — RBAC uses role alone, ABAC combines multiple attributes
- Leaving privileged access standing indefinitely instead of using just-in-time or ephemeral credentials to minimize exposure

---

## 📖 Further Reading

- [CompTIA Security+ (SY0-701) official exam objectives](https://www.comptia.org/certifications/security)

---

## 🔗 Related Notes

- [[Vulnerability Management and Security Monitoring]]
- [[Automation, Incident Response, and Digital Forensics]]
- [[Modifying Enterprise Capabilities to Enhance Security]] — drill note
- [[Implementing Identity and Access Management]] — drill note
- [[Security+ Codex]]
