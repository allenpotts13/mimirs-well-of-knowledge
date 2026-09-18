---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Network+
difficulty: Intermediate
tags:
  - network-plus
  - network-implementation
aliases:
  - Switching Configuration Choice
publish: true
permalink: comptia-network-n10-009/configuring-switching-technologies-for-a-scenario
---

# <span class="rune">ᚲ</span> Configuring Switching Technologies for a Scenario

> *If a trunk needs to carry more than one VLAN, 802.1Q tagging isn't optional — it's the whole mechanism.*

---

## 🎯 Problem

A scenario describes a switching requirement or symptom. Identify the correct feature or configuration.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "segment departments logically without new cabling" | VLANs |
| "one physical link must carry multiple VLANs" | 802.1Q trunk tagging |
| "VoIP phones need their own prioritized traffic lane" | Voice VLAN |
| "untagged traffic on a trunk port" | Native VLAN |
| "VLAN needs to communicate with another VLAN" | SVI (or an external router) for inter-VLAN routing |
| "combine multiple physical links for more bandwidth/redundancy" | Link aggregation |
| "prevent a loop in a redundant switched topology" | Spanning Tree Protocol |
| "large file transfers need less per-frame overhead" | Jumbo frames (raised MTU) |

---

## 🧠 Why This Pattern Works

Switching configuration questions test whether you understand that VLANs alone only *segment* — they never enable cross-VLAN communication without an SVI or router. Trunk-related settings (802.1Q, native VLAN, voice VLAN) all deal with how a single physical link carries multiple logical networks. STP and link aggregation solve two different redundancy problems: STP prevents loops from redundant paths, link aggregation turns redundant paths into combined bandwidth instead of blocking them.

---

## 🚨 Common Mistakes

- Expecting two VLANs to talk to each other with no SVI or router configured — pure VLAN segmentation blocks this by design
- Forgetting the native VLAN carries untagged traffic on a trunk — a common source of VLAN-hopping vulnerability if misconfigured
- Confusing link aggregation (combines links for bandwidth) with STP (blocks redundant links to prevent loops) — opposite goals for redundant paths
- Leaving MTU at default when jumbo frames were specifically required for a storage or backup network's efficiency

---

## 💡 Wisdom from Mímir

VLANs create walls. Only an SVI or router creates a door between them. If a scenario expects cross-VLAN traffic without mentioning either, something is missing from the design, not just the config.

---

## 🔗 Related Notes

- [[Routing and Switching Configuration]] — full concept writeup
- [[Network+ Codex]]
