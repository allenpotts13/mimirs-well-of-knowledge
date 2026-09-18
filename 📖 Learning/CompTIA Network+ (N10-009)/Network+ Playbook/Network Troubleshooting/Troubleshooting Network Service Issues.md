---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Network+
difficulty: Intermediate
tags:
  - network-plus
  - network-troubleshooting
aliases:
  - Troubleshooting Layer 3 Issues
publish: true
permalink: comptia-network-n10-009/troubleshooting-network-service-issues
---

# <span class="rune">ᚲ</span> Troubleshooting Network Service Issues

> *Nearly every "can't reach anything" ticket is a gateway, subnet mask, or duplicate-address problem in disguise.*

---

## 🎯 Problem

A scenario describes a routing, switching, or addressing service failure. Diagnose the likely cause.

---

## 🤔 Mental Model

| Symptom | Likely cause |
|---|---|
| Network loop / broadcast storm | STP misconfiguration or failure |
| Wrong root bridge elected | STP root bridge selection misconfigured |
| Device can't reach a VLAN's expected resources | Incorrect VLAN assignment |
| Traffic unexpectedly blocked between hosts | ACL misconfiguration |
| No route to a destination | Missing entry in the routing table, or no default route |
| DHCP clients failing to get an address | Address pool exhaustion |
| Clients using an unreachable gateway | Incorrect default gateway configured |
| Two devices conflict on the network | Duplicate IP address |
| Device can reach its own subnet but not others | Incorrect subnet mask |

---

## 🧠 Why This Pattern Works

Network service issues split into **switching-layer** problems (STP loops/root bridge issues, VLAN misassignment, ACLs) and **addressing/routing-layer** problems (missing routes, DHCP exhaustion, wrong gateway, duplicate IP, wrong subnet mask). A device that can reach its local subnet but nothing beyond it points to the gateway or subnet mask; a device that can't get an address at all points to DHCP; a whole segment flooding with traffic points to a Layer 2 loop.

---

## 🚨 Common Mistakes

- Diagnosing a duplicate IP address as a routing problem rather than checking for a second device using the same address
- Assuming a missing default route only affects internet access — it affects reachability to *any* network not explicitly in the routing table
- Overlooking DHCP scope exhaustion as the cause of intermittent "no address" symptoms during peak usage
- Treating an incorrect subnet mask as a cosmetic setting — it directly determines what a host considers "local" vs. "remote," breaking routing decisions silently

---

## 💡 Wisdom from Mímir

Ask "can this device reach its own subnet?" first. If no, suspect the local config (address, mask, duplicate). If yes but nothing beyond, suspect the gateway or the routing table.

---

## 🔗 Related Notes

- [[Network Troubleshooting Methodology and Overview]] — methodology and overview
- [[Network+ Codex]]
