---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA A+
difficulty: Beginner
tags:
  - a-plus-1201
  - hardware
aliases:
  - Power Supply Selection
publish: true
permalink: comptia-a-core-1-220-1201/selecting-the-right-power-supply-for-a-build
---

# <span class="rune">ᚲ</span> Selecting the Right Power Supply for a Build

> *Wattage headroom, not wattage minimum, is what a working build actually needs.*

---

## 🎯 Problem

A scenario describes a build's power requirements or a symptom that points to a power supply issue. Choose the right PSU spec or diagnose the problem.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "system randomly shuts down under heavy load (gaming, rendering)" | Insufficient PSU wattage for peak draw |
| "server must stay up through a single PSU failure" | Redundant power supply |
| "build needs only the cables actually used for a cleaner case" | Modular power supply |
| "minimize electricity cost/heat output for the same wattage" | Higher energy-efficiency rating |
| "system built for a region with 220-240V mains power" | Confirm PSU input voltage support/switch |
| "component needs 12V specifically (most modern CPUs/GPUs)" | Check the PSU's 12V rail capacity, not just total wattage |

---

## 🧠 Why This Pattern Works

A PSU's *total* wattage rating can be misleading if the load is concentrated on one rail (usually 12V, which modern CPUs and GPUs draw from most heavily). Random shutdowns under load — despite a PSU that looks "big enough" on paper — is the classic symptom of insufficient headroom or a weak rail, not a dead PSU. Redundant, modular, and efficiency-rated units solve availability, build-cleanliness, and running-cost problems respectively — three different problems that sound similar in a scenario.

---

## 🚨 Common Mistakes

- Sizing a PSU to *exactly* match calculated draw with no headroom — real draw spikes under load
- Assuming "modular" and "redundant" solve the same problem — modular is about cable management, redundant is about uptime
- Overlooking rail-specific capacity and looking only at the total wattage number on the box

---

## 💡 Wisdom from Mímir

When a build "randomly shuts down" only under load, suspect the power supply before the component that appears to be failing — starved power looks exactly like component failure from the user's side.

---

## 🔗 Related Notes

- [[System Assembly, Power, and Printers]] — full concept writeup
- [[Troubleshooting Motherboard, RAM, CPU, and Power Issues]] — drill note
- [[A+ Core 1 Codex]]
