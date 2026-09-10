---
type: concept
status: published
created: 2026-09-10
updated: 2026-09-10
technology: Azure
difficulty: Beginner
tags:
  - az-900
  - governance
  - cost-management
  - monitoring
aliases:
  - Azure Cost Management
  - Azure Policy
  - Azure Governance Tools
publish: true
---

# <span class="rune">ᛟ</span> Azure Management and Governance

> *Once things are running, this is how you keep the bill predictable, the rules enforced, and the health visible.*

---

## 🎯 Purpose

~30–35% of the exam. Covers cost estimation/management tools, governance/compliance tools, monitoring tools, and the different ways to actually deploy and manage Azure resources.

---

## 🧠 Key Ideas

- Cost tools split into "before you deploy" (calculators) and "after you deploy" (Cost Management)
- Governance tools enforce rules (Policy) vs. grant permissions (RBAC, covered in [[Identity, Access, and Security]]) — different jobs
- Monitoring tools split into "is my stuff healthy" (Monitor, Advisor) and "is Azure itself healthy" (Service Health)
- Every management tool (Portal, CLI, PowerShell, ARM templates) ultimately calls the same underlying Azure Resource Manager API

---

## ⚙️ How It Works

### Cost management tools

| Tool | When you use it | Purpose |
|---|---|---|
| **Pricing calculator** | Before deploying | Estimate cost of a planned configuration |
| **Total Cost of Ownership (TCO) calculator** | Before migrating | Compare on-prem cost vs. estimated Azure cost |
| **Azure Cost Management + Billing** | After deploying | Monitor, analyze, and set budgets/alerts on actual spend |
| **Tags** | Ongoing | Metadata (key-value pairs) applied to resources for cost allocation, organization, automation |

Factors that affect Azure cost: resource type, region, consumption/usage tier, bandwidth (egress is billed, ingress is usually free).

### Governance tools

| Tool | Purpose |
|---|---|
| **Azure Policy** | Enforces organizational rules/standards (e.g. "only allow VMs in East US") — evaluates and can auto-remediate non-compliant resources |
| **Azure RBAC** | Grants permissions (who can do what) — see [[Identity, Access, and Security]] |
| **Resource locks** | Prevents deletion/modification regardless of RBAC — see [[Identity, Access, and Security]] |
| **Azure Blueprints** | Packages a set of policies, role assignments, and resource templates for repeatable environment setup |
| **Management groups** | Applies governance (Policy, RBAC) across many subscriptions at once |

> Policy vs. RBAC in one line: **RBAC controls what you're *allowed* to do; Policy controls what's *allowed to exist*.**

### Monitoring and health tools

| Tool | Purpose |
|---|---|
| **Azure Monitor** | Collects, analyzes, and acts on telemetry (metrics, logs) from your resources |
| **Azure Advisor** | Personalized recommendations across cost, security, reliability, performance, operational excellence |
| **Azure Service Health** | Reports on the health of Azure services themselves — outages, planned maintenance, health advisories affecting your resources |

> Advisor recommends changes to *your* resources; Service Health reports on the health of *Azure's* infrastructure.

### Privacy, compliance & data residency

- **Microsoft Purview Compliance Manager** — assesses compliance risk and tracks regulatory requirements
- **Service Trust Portal** — provides audit reports, compliance guides, and trust documentation
- **Data residency** — some regions/services let you control where data is physically stored to meet legal requirements

### Deployment and management tools

| Tool | Interface |
|---|---|
| **Azure Portal** | Web-based GUI |
| **Azure Cloud Shell** | Browser-based CLI (Bash or PowerShell), no local install needed |
| **Azure CLI** | Cross-platform command-line tool |
| **Azure PowerShell** | PowerShell module for Azure management |
| **ARM templates** | Declarative JSON (or Bicep) for infrastructure as code |

All of these ultimately go through **Azure Resource Manager (ARM)** — which is why RBAC, tags, and policies apply consistently no matter which tool was used to create a resource.

---

## 💻 Examples

```text
"Estimate what a proposed 3-VM architecture will cost before deploying it."
→ Pricing calculator

"Enforce that no one can create a storage account outside allowed regions."
→ Azure Policy

"Get a personalized recommendation to resize an underutilized VM."
→ Azure Advisor

"Check whether an Azure outage in East US is affecting my resources."
→ Azure Service Health

"Deploy infrastructure repeatably from a declarative JSON file."
→ ARM template
```

---

## 🚨 Common Mistakes

- Confusing Azure Policy (governs what can exist) with RBAC (governs who can act)
- Confusing Azure Advisor (recommendations about your resources) with Azure Service Health (status of Azure itself)
- Picking the TCO calculator for a "what will this new Azure deployment cost" question — that's the Pricing calculator; TCO is specifically for comparing against existing on-prem costs

---

## 📖 Further Reading

- [Microsoft Learn: Describe Azure management and governance](https://learn.microsoft.com/en-us/training/paths/microsoft-azure-fundamentals-describe-azure-management-governance/)

---

## 🔗 Related Notes

- [[Identity, Access, and Security]]
- [[Azure RBAC vs Azure Policy vs Resource Locks]] — drill note
- [[Picking the Right Cost Management Tool]] — drill note
- [[AZ-900 Codex]]
