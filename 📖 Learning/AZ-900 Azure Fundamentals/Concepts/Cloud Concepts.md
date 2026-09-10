---
type: concept
status: published
created: 2026-09-10
updated: 2026-09-10
technology: Azure
difficulty: Beginner
tags:
  - az-900
  - cloud-concepts
aliases:
  - Cloud Computing Basics
  - Cloud Service Models
publish: true
---

# <span class="rune">ᛟ</span> Cloud Concepts

> *What "the cloud" actually means, the three ways to buy it, and why businesses migrate in the first place.*

---

## 🎯 Purpose

~25–30% of the exam. This domain is pure vocabulary — cloud models, service models, and the business benefits Microsoft wants you to be able to articulate. No Azure-specific service names yet; that's the next domain.

---

## 🧠 Key Ideas

- Cloud computing = delivering compute resources over the internet, paid for as you use them
- Three deployment models: public, private, hybrid (and multi-cloud as a related idea)
- Three service models: IaaS, PaaS, SaaS — each hands you a different slice of the stack to manage
- CapEx vs OpEx is the financial argument for cloud adoption
- The shared responsibility model shifts *some* security/maintenance burden to the provider, never all of it

---

## ⚙️ How It Works

### Cloud deployment models

| Model | Description | Example use case |
|---|---|---|
| **Public cloud** | Resources owned/operated by a third-party provider, shared infrastructure | Startups, variable workloads, no upfront hardware |
| **Private cloud** | Dedicated infrastructure for one organization, on-prem or hosted | Strict compliance, legacy systems, full control |
| **Hybrid cloud** | Combines public + private, workloads can move between them | Gradual migration, data residency requirements |
| **Multi-cloud** | Uses more than one public cloud provider | Avoiding vendor lock-in, best-of-breed services |

### Cloud service models

```
IaaS ─────────────── PaaS ─────────────── SaaS
(most control,        (middle ground)      (least control,
 most management)                           zero management)

You manage:           You manage:           You manage:
 OS, runtime,           app + data            just your data/config
 middleware, app,
 data
```

| Model | You manage | Provider manages | Example |
|---|---|---|---|
| **IaaS** (Infrastructure as a Service) | OS, runtime, middleware, apps, data | Physical hardware, virtualization, networking | Azure Virtual Machines |
| **PaaS** (Platform as a Service) | Apps, data | OS, runtime, middleware, infrastructure | Azure App Service |
| **SaaS** (Software as a Service) | Data, user config | Everything else | Microsoft 365, Outlook |

### Shared responsibility model

Security and management responsibility is always split between you and the cloud provider — it just shifts where the line sits as you move from IaaS → PaaS → SaaS. The provider always owns the physical facility; you always own your data and access management, no matter the model.

### Cloud economics: CapEx vs OpEx

| | CapEx (on-prem) | OpEx (cloud) |
|---|---|---|
| Spending pattern | Large upfront investment | Pay-as-you-go, ongoing |
| Predictability | Fixed, sunk cost | Scales with usage |
| Risk | Over/under-provisioning risk | Elastic — pay only for what's used |

- **Consumption-based model**: pay only for the resources you use, when you use them — the defining financial trait of cloud computing

### Key cloud benefits (Microsoft's list — know these by name)

| Benefit | Meaning |
|---|---|
| **High availability** | Designed to stay operational with minimal downtime |
| **Scalability** | Ability to increase/decrease resources to meet demand |
| **Elasticity** | Automatically scales resources up/down in response to load |
| **Agility** | Quickly develop, test, and deploy applications |
| **Fault tolerance** | Ability to remain operational even when components fail |
| **Disaster recovery** | Ability to restore operations after a major failure |

- **Scalability vs elasticity**: scalability is the *capability* to add resources (often manual or planned); elasticity is *automatic*, on-demand scaling in response to real-time load

---

## 💻 Examples

```text
"A hospital keeps patient records on-site for compliance but bursts
overflow analytics workloads to Azure during peak periods."
→ Hybrid cloud

"A company runs VMs it fully patches and configures itself, but doesn't
own the physical servers."
→ IaaS

"An e-commerce site automatically adds server capacity during a flash sale
and scales back down afterward, with no manual intervention."
→ Elasticity
```

---

## 🚨 Common Mistakes

- Confusing scalability (can scale) with elasticity (automatically scales)
- Assuming SaaS means "no responsibility at all" — you still manage your data and user access
- Mixing up hybrid cloud (public + private together) with multi-cloud (multiple public providers)

---

## 📖 Further Reading

- [Microsoft Learn: Describe cloud concepts](https://learn.microsoft.com/en-us/training/paths/microsoft-azure-fundamentals-describe-cloud-concepts/)

---

## 🔗 Related Notes

- [[Core Azure Architecture and Services]]
- [[IaaS vs PaaS vs SaaS - Spotting the Service Model]] — drill note
- [[CapEx vs OpEx and the Economics of Cloud]] — drill note
- [[AZ-900 Codex]]
