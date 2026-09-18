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
  - Troubleshooting Layer 1/2 Issues
publish: true
permalink: comptia-network-n10-009/troubleshooting-cabling-and-physical-interface-issues
---

# <span class="rune">ᚲ</span> Troubleshooting Cabling and Physical Interface Issues

> *An interface counter is Layer 1/2 confessing exactly what's wrong — if you know how to read it.*

---

## 🎯 Problem

A scenario describes a cabling, interface, or physical hardware symptom. Diagnose the likely cause.

---

## 🤔 Mental Model

| Symptom | Likely cause |
|---|---|
| CRC errors (rising counter) | Signal/framing errors — bad cable, interference, or a failing NIC |
| Runts | Frames smaller than the minimum — often a duplex mismatch or bad NIC |
| Giants | Frames larger than the maximum — misconfigured MTU or a faulty device |
| Drops | Interface can't process traffic fast enough, or a queuing issue |
| Port status: error disabled | Switch auto-shutdown due to a violation (e.g., port security) |
| Port status: administratively down | Manually disabled by an admin |
| Crosstalk / interference | Improper cable routing near electrical noise sources |
| Attenuation | Signal weakening over distance — cable run may exceed spec length |
| Improper termination | Cable wired incorrectly at the connector |
| TX/RX transposed | Transmit and receive pins swapped — common on mismatched cable types |
| PoE power budget exceeded | Switch can't supply enough power to all connected PoE devices |
| Transceiver mismatch | Wrong SFP/QSFP type for the port or the fiber type |

---

## 🧠 Why This Pattern Works

Physical/interface troubleshooting reads like a translation exercise: each interface counter or port state maps to one specific physical-layer cause. CRC errors and runts/giants point to signal or framing problems (bad cable, interference, mismatched duplex/MTU); port status codes (error disabled, administratively down) point to switch-side configuration or security responses rather than the cable itself; PoE and transceiver issues are hardware-compatibility problems specific to powered or optical links.

---

## 🚨 Common Mistakes

- Assuming a rising CRC error count means the switch is failing — it usually means the cable, connector, or interference is the actual problem
- Confusing "error disabled" (switch shut the port down automatically, often due to a security violation) with "administratively down" (an admin manually disabled it) — different root causes, different fixes
- Overlooking cable length/attenuation limits when a long run shows intermittent signal issues
- Installing a transceiver rated for the wrong fiber type (single-mode vs. multimode) and assuming the link should just work

---

## 💡 Wisdom from Mímir

Before touching switch configuration, always check the physical layer first: cable, connector, port status LED, and interface counters. Most "network is down" tickets die right here.

---

## 🔗 Related Notes

- [[Network Troubleshooting Methodology and Overview]] — methodology and overview
- [[Network+ Codex]]
