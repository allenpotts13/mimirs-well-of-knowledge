---
type: cheat-sheet
status: published
created: 2026-09-10
updated: 2026-09-10
technology: Azure
tags:
  - az-900
  - cheat-sheet
aliases:
  - AZ-900 Quick Reference
publish: true
---

# ⚡ AZ-900 Cheat Sheet

> *Last-mile reference. If you can fill in every row of this from memory, you're ready.*

---

## Cloud deployment models

| Model | Meaning |
|---|---|
| Public | Third-party, shared infrastructure |
| Private | Dedicated to one org |
| Hybrid | Public + private combined |
| Multi-cloud | Multiple public providers |

## Service models

| Model | You manage | Example |
|---|---|---|
| IaaS | OS, runtime, middleware, app, data | Virtual Machines |
| PaaS | App, data | App Service |
| SaaS | Data, config only | Microsoft 365 |

## Cloud economics

- **CapEx** = upfront hardware purchase (on-prem)
- **OpEx** = pay-as-you-go, ongoing (cloud)
- **Consumption-based model** = pay only for what you use

## Key benefits

| Term | Meaning |
|---|---|
| High availability | Stays operational, minimal downtime |
| Scalability | Can add/remove resources |
| Elasticity | *Automatically* scales with demand |
| Agility | Fast dev/test/deploy |
| Fault tolerance | Keeps running despite component failure |
| Disaster recovery | Restores operations after major failure |

## Physical structure

```
Geography → Region → Availability Zone → Datacenter
```

- **Region pair** = 2 regions, same geography, sequential updates, DR partners
- **Sovereign region** = isolated instance for compliance (Gov, China)

## Organizational hierarchy

```
Management Group → Subscription → Resource Group → Resource
```
Every resource: exactly 1 resource group, 1 subscription.

## Compute services

| Service | Model | Fit |
|---|---|---|
| Virtual Machines | IaaS | Full control, lift-and-shift |
| VM Scale Sets | IaaS | Auto-scaling identical VMs |
| Container Instances (ACI) | Containers | Single container, no orchestration |
| AKS | Containers | Managed Kubernetes, multi-container |
| App Service | PaaS | Web apps/APIs, no server mgmt |
| Functions | Serverless | Event-triggered, pay-per-execution |
| Virtual Desktop | VDI | Remote desktop delivery |

## Networking

| Service | Purpose |
|---|---|
| VNet | Private network |
| VPN Gateway | Encrypted, over public internet |
| ExpressRoute | Private, dedicated circuit |
| Load Balancer | Layer 4, regional traffic distribution |
| Application Gateway | Layer 7, URL routing + WAF |
| Azure CDN | Edge caching |

## Identity & access

| Term | Meaning |
|---|---|
| Microsoft Entra ID | Identity/directory platform |
| MFA | 2+ verification factors |
| Conditional Access | Context-based sign-in rules |
| External Identities (B2B/B2C) | Partner / customer identity |
| SSO | One login, many apps |
| Azure RBAC | Role + scope = permissions |
| Resource lock | CanNotDelete / ReadOnly — overrides RBAC |

RBAC scope inheritance: **Management Group → Subscription → Resource Group → Resource** (assignments flow down)

## Security philosophy

- **Defense in depth**: Data → App → Compute → Network → Perimeter → Identity → Physical
- **Zero Trust**: never trust, always verify — verify explicitly, least privilege, assume breach
- **Microsoft Defender for Cloud**: secure score + recommendations

## Governance vs permissions vs locks

| Tool | Answers |
|---|---|
| RBAC | Who can do what |
| Azure Policy | What's allowed to exist/be configured |
| Resource lock | Can this be deleted/changed, period |
| Blueprints | Packaged policies + roles + templates |

## Cost tools

| Tool | When |
|---|---|
| Pricing calculator | Before deploying — estimate |
| TCO calculator | Before migrating — compare to on-prem |
| Cost Management + Billing | After deploying — track actual spend, budgets |
| Tags | Ongoing — metadata for allocation |

## Monitoring tools

| Tool | Watches |
|---|---|
| Azure Monitor | Telemetry from your resources |
| Azure Advisor | Recommendations for your resources |
| Azure Service Health | Health of Azure itself |

## Deployment tools

Portal (GUI) · Cloud Shell (browser CLI) · Azure CLI · Azure PowerShell · ARM templates (IaC) — all go through **Azure Resource Manager (ARM)**

---

## 🔗 Related Notes

- [[AZ-900 Codex]]
