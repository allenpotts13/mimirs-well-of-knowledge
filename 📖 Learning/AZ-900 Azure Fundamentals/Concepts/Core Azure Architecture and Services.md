---
type: concept
status: published
created: 2026-09-10
updated: 2026-09-10
technology: Azure
difficulty: Beginner
tags:
  - az-900
  - azure-architecture
  - compute
  - networking
  - storage
aliases:
  - Azure Regions and Resource Hierarchy
  - Azure Compute Services
publish: true
---

# 📚 Core Azure Architecture and Services

> *How Azure organizes the planet's datacenters into something you can point resources at, and the core building blocks — compute, networking, storage — you deploy into them.*

---

## 🎯 Purpose

The largest domain, ~35–40% of the exam. Two halves: (1) the physical/organizational hierarchy Azure resources live in, and (2) the core service catalog — compute, networking, storage — at a "what is it and when would I use it" level, not a hands-on level.

---

## 🧠 Key Ideas

- Azure's physical footprint: geographies → regions → availability zones → datacenters
- Azure's organizational hierarchy: management groups → subscriptions → resource groups → resources
- Compute options form a spectrum from "you manage everything" (VMs) to "you manage nothing but code" (Functions)
- Networking connects and secures everything; storage persists everything
- Every resource lives in exactly one resource group, and resource groups live in exactly one subscription

---

## ⚙️ How It Works

### Physical structure

| Term | Definition |
|---|---|
| **Geography** | A discrete market, typically containing 2+ regions, meeting data residency/compliance boundaries |
| **Region** | A set of datacenters deployed within a latency-defined perimeter, connected by a low-latency network |
| **Availability zone (AZ)** | A physically separate location within a region, with independent power/cooling/networking — protects against datacenter-level failure |
| **Region pair** | Two regions in the same geography, paired for disaster recovery — updates are rolled out to paired regions sequentially, never both at once |
| **Sovereign region** | An isolated instance of Azure for specific compliance/national requirements (e.g. Azure Government, Azure China) |

### Organizational hierarchy (top to bottom)

```
Management Group  →  Subscription  →  Resource Group  →  Resource
 (governance             (billing +          (logical               (the actual
  boundary across         access boundary)    container for          thing: VM,
  many subscriptions)                          related resources)     storage acct...)
```

| Level | Purpose |
|---|---|
| **Management group** | Groups subscriptions together to apply governance (policy, RBAC) at scale |
| **Subscription** | Billing boundary + access control boundary; every resource belongs to exactly one |
| **Resource group** | Logical container for resources that share a lifecycle (deploy/delete together) |
| **Resource** | An individual manageable item: a VM, storage account, VNet, etc. |

- **Azure Resource Manager (ARM)** is the deployment/management layer all tools (Portal, CLI, PowerShell, ARM templates) go through — it's what enforces RBAC and applies tags/policies consistently regardless of which tool you used

### Compute services

| Service | What it is | Best for |
|---|---|---|
| **Virtual Machines (VMs)** | IaaS — full control VM | Lift-and-shift, custom OS/software requirements |
| **Virtual Machine Scale Sets** | Auto-scaling group of identical VMs | Large workloads needing elastic capacity |
| **App Service** | PaaS for hosting web apps/APIs | Web apps without managing infrastructure |
| **Azure Functions** | Serverless, event-driven, pay-per-execution | Small, event-triggered pieces of code |
| **Container Instances (ACI)** | Run a single container without orchestration | Quick, isolated container workloads |
| **Azure Kubernetes Service (AKS)** | Managed Kubernetes orchestration | Complex, multi-container applications at scale |
| **Azure Virtual Desktop** | Virtualized desktop/app experience delivered from the cloud | Remote desktop infrastructure (VDI) |

Spectrum: **VMs** (most control) → **App Service/AKS** (middle) → **Functions** (least control, zero server management).

### Networking services

| Service | Purpose |
|---|---|
| **Virtual Network (VNet)** | Private network in Azure — segments and secures resources |
| **VPN Gateway** | Encrypted connection between Azure and on-prem over the public internet |
| **ExpressRoute** | Private, dedicated connection between on-prem and Azure (doesn't traverse the public internet) |
| **Azure DNS** | Hosts DNS domains in Azure |
| **Load Balancer** | Distributes traffic across VMs at layer 4 (transport) within a region |
| **Application Gateway** | Layer 7 (application) load balancing — URL-based routing, SSL termination, WAF |
| **Azure CDN** | Caches content at edge locations close to users for faster delivery |

> VPN Gateway vs ExpressRoute: VPN Gateway is encrypted-over-public-internet; ExpressRoute is a private, dedicated circuit that never touches the public internet — faster and more reliable, but costs more and takes longer to provision.

### Storage services (overview — see [[Non-Relational Data on Azure|DP-900's storage note]] for the deep dive on tiers/redundancy)

| Service | Type |
|---|---|
| **Blob storage** | Object storage for unstructured data |
| **Disk storage** | Managed disks for VMs |
| **File storage** | Managed SMB/NFS file shares |
| **Queue storage** | Message queuing |
| **Table storage** | NoSQL key-value store |

Migration tools worth recognizing by name: **AzCopy** (command-line bulk copy), **Azure Storage Explorer** (GUI tool), **Azure Migrate** (assess/migrate on-prem workloads), **Import/Export service** (ship physical disks for very large datasets).

---

## 💻 Examples

```text
"Deploy identical resources that must fail together, updated as a unit."
→ Resource group

"Apply a compliance policy across 30 subscriptions in one action."
→ Management group

"Run code only when a file is uploaded, and pay only for the milliseconds
it executes."
→ Azure Functions

"Connect on-prem to Azure over a private circuit that never touches the
public internet."
→ ExpressRoute
```

---

## 🚨 Common Mistakes

- Confusing a region pair (DR partner regions) with an availability zone (datacenters within one region)
- Assuming resources can belong to multiple resource groups — they can't, exactly one
- Picking VPN Gateway when the scenario explicitly asks for a connection that avoids the public internet — that's ExpressRoute
- Mixing up Load Balancer (layer 4, regional) with Application Gateway (layer 7, URL-based routing + WAF)

---

## 📖 Further Reading

- [Microsoft Learn: Describe Azure architecture and services](https://learn.microsoft.com/en-us/training/paths/microsoft-azure-fundamentals-describe-azure-architecture-services/)

---

## 🔗 Related Notes

- [[Cloud Concepts]]
- [[Identity, Access, and Security]]
- [[Choosing the Right Azure Compute Service]] — drill note
- [[Regions vs Availability Zones vs Region Pairs]] — drill note
- [[AZ-900 Codex]]
