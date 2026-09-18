---
type: concept
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Network+
difficulty: Beginner
tags:
  - network-plus
  - network-operations
aliases:
  - Network+ Documentation
  - Network+ Disaster Recovery
publish: true
permalink: comptia-network-n10-009/network-documentation-change-management-and-disaster-recovery
---

# <span class="rune">ᛟ</span> Network Documentation, Change Management, and Disaster Recovery

> *An undocumented network is a network no one can safely change, and a disaster recovery plan no one has tested is just a hope.*

---

## 🎯 Purpose

Covers 3.1 and 3.3 of the 19%-weighted Network Operations domain: organizational processes/documentation practices, and disaster recovery concepts.

---

## 🧠 Key Ideas

- Network documentation exists at multiple layers: **physical** (rack/cable diagrams), **logical** (Layer 1/2/3 network diagrams), and **asset-level** (inventory, licensing, warranty)
- **Configuration management** distinguishes production config (what's live), backup config (saved copies), and baseline/golden config (the known-good standard to compare against)
- **Life-cycle management** (EOL/EOS) determines when hardware/software must be replaced or stops receiving support — a compliance and security issue, not just a budget one
- DR metrics quantify recovery targets: **RPO** (how much data loss is acceptable) and **RTO** (how much downtime is acceptable) are set *before* a disaster, not during one
- DR site tiers (cold/warm/hot) trade off cost against how quickly they can take over

---

## ⚙️ How It Works

### Documentation types

| Type | Captures |
|---|---|
| Physical vs. logical diagrams | Physical layout vs. logical network structure |
| Rack diagrams | Equipment placement within racks |
| Cable maps | Physical cable runs and termination points |
| Network diagrams (L1/L2/L3) | Physical links, VLANs/switching, IP/routing structure |
| Asset inventory | Hardware, software, licensing, warranty support |
| IPAM | IP Address Management — tracks allocation across the network |
| SLA | Documented service commitments |
| Wireless survey/heat map | RF coverage and interference documentation |

### Life-cycle and change management

| Term | Meaning |
|---|---|
| EOL (End-of-Life) | Vendor stops selling/developing the product |
| EOS (End-of-Support) | Vendor stops providing support/patches — a security risk if still in use |
| Change management | Formal request/tracking process for network changes |
| Configuration management | Production vs. backup vs. baseline/golden configuration |

### Disaster recovery concepts

| Metric | Meaning |
|---|---|
| RPO (Recovery Point Objective) | Maximum acceptable data loss, measured in time |
| RTO (Recovery Time Objective) | Maximum acceptable downtime |
| MTTR (Mean Time To Repair) | Average time to fix a failure |
| MTBF (Mean Time Between Failures) | Average time between failures — a reliability measure |

| DR site | Readiness | Cost |
|---|---|---|
| Cold site | Space only, no equipment/data ready | Lowest |
| Warm site | Some equipment, data not fully current | Middle |
| Hot site | Fully equipped, near-real-time data replication | Highest |

**High-availability approaches:** active-active (all nodes serve traffic simultaneously) vs. active-passive (standby node takes over on failure). **DR testing:** tabletop exercises (discussion-based walkthrough) vs. validation tests (actual failover testing).

---

## 💻 Examples

```text
"A business can tolerate at most 15 minutes of data loss in a disaster."
→ RPO of 15 minutes

"A business can tolerate at most 4 hours of downtime before major impact."
→ RTO of 4 hours

"A backup site has hardware ready but requires hours to bring data current
before taking over."
→ Warm site

"A DR plan is tested by walking through the response steps as a
discussion, without actually failing anything over."
→ Tabletop exercise

"Network hardware still in use has stopped receiving security patches
from the vendor."
→ Past End-of-Support (EOS) — a security risk requiring remediation
```

---

## 🚨 Common Mistakes

- Confusing RPO (data loss tolerance) with RTO (downtime tolerance) — one measures data, the other measures time to restore service
- Assuming EOL and EOS happen at the same time — EOS (support ends) often comes after EOL (sales end)
- Treating a cold site as "good enough" without accounting for the real time needed to make it operational
- Skipping DR testing because the plan "looks complete on paper" — an untested plan is unverified, same as an untested backup

---

## 📖 Further Reading

- [CompTIA Network+ (N10-009) official exam objectives](https://www.comptia.org/certifications/network)

---

## 🔗 Related Notes

- [[Network Monitoring and Access Management]]
- [[Choosing the Right Disaster Recovery Approach]] — drill note
- [[Network+ Codex]]
