---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Network+
difficulty: Beginner
tags:
  - network-plus
  - networking-concepts
aliases:
  - Transmission Media Choice
publish: true
permalink: comptia-network-n10-009/choosing-the-right-transmission-media-and-connector
---

# <span class="rune">ᚲ</span> Choosing the Right Transmission Media and Connector

> *Distance and bandwidth requirements eliminate most of the wrong answers before you even look at connector shapes.*

---

## 🎯 Problem

A scenario describes a distance, bandwidth, or environmental requirement for a physical link. Choose the correct media and connector.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "very long distance, immune to electrical interference" | Single-mode fiber |
| "shorter distance, cheaper than single-mode, still fiber-speed" | Multimode fiber |
| "standard copper Ethernet run within a building" | UTP, RJ45 |
| "copper run through an area needing extra EMI shielding" | STP |
| "must run through air ducts/plenum spaces" | Plenum-rated cable |
| "very short, high-bandwidth copper link between adjacent devices" | DAC/twinaxial cable |
| "legacy analog phone line connection" | RJ11 |
| "cable TV or broadband coax connection" | F-type connector |
| "legacy video/security camera coax" | BNC connector |
| "high-density fiber trunk between switches" | MPO connector |

---

## 🧠 Why This Pattern Works

Media choice starts with two questions: how far, and how much interference resistance is needed? Fiber wins on both distance and immunity to EMI but costs more; copper is cheaper and fine for short, controlled-environment runs. Connector choice then follows directly from the media already chosen — RJ45 only pairs with twisted pair, SC/LC/ST/MPO only pair with fiber, and F-type/BNC only pair with coax.

---

## 🚨 Common Mistakes

- Choosing multimode fiber for extremely long-haul links — single-mode is required past multimode's effective distance
- Running standard (non-plenum) cable through an air handling space — a fire code violation, not just a performance issue
- Confusing DAC/twinaxial (short, high-bandwidth, low-cost copper for adjacent rack equipment) with standard patch cabling
- Assuming any RJ-style connector is interchangeable — RJ11 (phone) and RJ45 (Ethernet) are physically different sizes

---

## 💡 Wisdom from Mímir

Distance first, interference second, cost third — in that order, nearly every media/connector question resolves itself.

---

## 🔗 Related Notes

- [[Ports, Protocols, Media, and Topologies]] — full concept writeup
- [[Network+ Codex]]
