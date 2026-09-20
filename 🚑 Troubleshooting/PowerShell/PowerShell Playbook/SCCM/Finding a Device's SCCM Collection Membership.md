---
type: powershell-pattern
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Advanced
tags:
  - sccm
  - configuration-manager
  - collections
aliases:
  - Get-CMDeviceCollection
  - SCCM Collection Membership PowerShell
publish: true
permalink: powershell/finding-a-devices-sccm-collection-membership
---

# <span class="rune">ᚲ</span> Finding a Device's SCCM Collection Membership

> *Every deployment targets a collection, never a device directly — which means "why didn't this machine get the deployment" almost always has its answer in collection membership, not in the deployment itself.*

---

## 🎯 Problem

An application or configuration deployment isn't reaching a specific machine, and the underlying cause needs to be traced back to collection membership — SCCM deployments always target collections, never individual devices directly.

More generally, this pattern answers:
> Which SCCM collections does this device currently belong to, and is it a member of the specific collection a given deployment is targeting?

---

## 🤔 Mental Model

Treat SCCM collections like mailing lists a deployment is addressed to — a deployment sent to "IT Department" never reaches someone whose subscription lapsed or who was never added to that list, no matter how correctly the deployment itself was configured. Checking collection membership is checking the mailing list itself, before assuming anything is wrong with the message being sent to it.

---

## 🧠 Why This Pattern Works

The `ConfigurationManager` PowerShell module (installed alongside the SCCM console) provides `Get-CMDevice`/`Get-CMCollectionMember` cmdlets that query the SCCM site database directly for collection membership — this requires connecting to the site server's own PowerShell drive, distinct from every other pattern in this Codex which operates against a target machine's local or remote CIM/WMI data. Collection membership is either **direct** (manually added) or **query-based/dynamic** (automatically included via a membership rule), and a device unexpectedly missing from a collection is very often failing a dynamic query's specific criteria rather than having been deliberately excluded.

---

## 💻 PowerShell Solution

```powershell
# Connect to the SCCM site's PowerShell drive (run from a machine with the ConfigurationManager module, e.g., the console machine)
Import-Module "$($ENV:SMS_ADMIN_UI_PATH)\..\ConfigurationManager.psd1"
$siteCode = (Get-PSDrive -PSProvider CMSite).Name
Set-Location "$siteCode`:"

# All collections a specific device belongs to
Get-CMDevice -Name 'WS042' | Get-CMDeviceCollectionMembership |
    Select-Object CollectionName

# Checking whether a device is a member of one SPECIFIC collection a deployment targets
$isMember = (Get-CMCollectionMember -CollectionName 'All Contoso Workstations' -Name 'WS042')
if ($isMember) {
    Write-Output "WS042 IS a member of the target collection"
} else {
    Write-Output "WS042 is NOT a member — this explains the missing deployment"
}
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Connect to the SCCM site's PowerShell drive

```powershell
Import-Module "$($ENV:SMS_ADMIN_UI_PATH)\..\ConfigurationManager.psd1"
Set-Location "$siteCode`:"
```

Unlike most other patterns in this Codex, SCCM collection queries run against the **site database**, not the target device itself — this requires the `ConfigurationManager` module, typically only available on a machine with the SCCM console installed.

### Step 2 — List every collection the device currently belongs to

```powershell
Get-CMDevice -Name 'WS042' | Get-CMDeviceCollectionMembership
```

Gives the full picture of every collection membership at once — useful when the specific target collection for the deployment in question isn't already known.

### Step 3 — Check membership in one specific known target collection

```powershell
Get-CMCollectionMember -CollectionName 'All Contoso Workstations' -Name 'WS042'
```

Directly answers the actual troubleshooting question once the deployment's target collection is known — a fast, specific check rather than reviewing the device's entire membership list.

---

## 🚀 Common Use Cases

- Diagnosing "this machine never got the deployment" by confirming whether it's even in the targeted collection at all
- Investigating why a device unexpectedly dropped out of a dynamic/query-based collection after a property change (department, OU, installed software)
- Confirming a device was successfully added to a collection after a manual addition or updated query rule
- Auditing which collections (and therefore which policies/deployments) apply to a specific machine, as part of a broader troubleshooting investigation

---

## ⚖️ Alternatives

### Checking why a device fails a dynamic collection's query rule

```powershell
Get-CMCollectionQueryMembershipRule -CollectionName 'All Contoso Workstations' |
    Select-Object -ExpandProperty QueryExpression
```

Reveals the actual WQL query defining a dynamic collection's membership — useful for understanding *why* a device might not qualify, beyond simply confirming that it currently doesn't.

### Adding a device directly to a collection as an immediate workaround

```powershell
Add-CMDeviceCollectionDirectMembershipRule -CollectionName 'All Contoso Workstations' -ResourceId (Get-CMDevice -Name 'WS042').ResourceID
```

A direct membership rule bypasses a dynamic query entirely for one specific device — useful as an immediate, targeted fix while a broader query rule issue is separately investigated and corrected.

---

## ⚠️ Common Mistakes

- Investigating the deployment configuration itself at length before checking the far simpler explanation: the device was never actually a member of the target collection.
- Not accounting for collection membership evaluation delay — dynamic collections re-evaluate on a schedule, not instantly, so a device that should now qualify might not reflect that for some time after the underlying change.
- Assuming the `ConfigurationManager` module is available on any machine — it's specifically tied to having the SCCM console (or the module installed independently) present, unlike the CIM-based patterns elsewhere in this Codex that work from any machine.
- Adding a device via a direct membership rule as a permanent fix for what's actually a broken dynamic query — the direct rule works, but leaves the underlying query problem unaddressed for every other device that might be affected the same way.

---

## 📖 Further Reading

- Microsoft Learn: "Introduction to collections in Configuration Manager"
- Microsoft Learn: "ConfigurationManager PowerShell cmdlet reference"

---

## 💡 Wisdom from Mímir

Before investigating a deployment's own configuration, always check collection membership first — deployments target collections, never devices, and a huge share of "why didn't this reach the machine" tickets are actually answered at the mailing-list level, not the message level.

---

## 🔗 Related Notes

- [[Triggering a Machine Policy Retrieval and Evaluation Cycle]]
- [[Forcing an Application Deployment to Install Immediately]]
- [[Checking SCCM Client Health on a Remote Computer]]
- [[Finding Stale or Inactive AD Computer Accounts]]
