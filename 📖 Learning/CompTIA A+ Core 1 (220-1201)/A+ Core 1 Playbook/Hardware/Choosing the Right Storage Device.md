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
  - Storage Device Selection
publish: true
permalink: comptia-a-core-1-220-1201/choosing-the-right-storage-device
---

# <span class="rune">ᚲ</span> Choosing the Right Storage Device

> *Interface decides the speed ceiling before form factor ever matters.*

---

## 🎯 Problem

A scenario describes a performance or capacity requirement. Choose the right storage type, interface, or RAID level.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "cheapest possible bulk storage, speed not critical" | HDD |
| "fastest possible storage available, has an M.2 slot" | NVMe SSD |
| "SSD performance but only a SATA port available" | SATA SSD |
| "portable storage carried between locations" | Flash drive / external drive |
| "array must survive a single drive failure with minimal capacity loss" | RAID 1 (small array) or RAID 5 (larger array) |
| "array must survive two simultaneous drive failures" | RAID 6 |
| "array needs maximum raw performance, redundancy not required" | RAID 0 |

---

## 🧠 Why This Pattern Works

Storage decisions layer the same way RAM does: interface (SATA vs. NVMe/PCIe) sets the performance ceiling, form factor (2.5"/3.5"/M.2/mSATA) sets what physically fits, and RAID level (when arrays are involved) trades capacity for fault tolerance. NVMe is dramatically faster than SATA specifically because it talks to the CPU over PCIe lanes instead of the older, slower SATA command protocol.

---

## 🚨 Common Mistakes

- Assuming any M.2 drive is automatically NVMe — M.2 is just a form factor; M.2 SATA drives exist and are much slower than M.2 NVMe
- Picking RAID 0 when the scenario mentions any fault-tolerance requirement — RAID 0 has zero redundancy
- Forgetting RAID 5's usable capacity is (n-1)/n drives, not 50% flat like RAID 1 — capacity math scales with drive count

---

## 💡 Wisdom from Mímir

Ask two questions in order: "does this need to survive a drive failure?" then "how fast does it need to be?" The first question picks RAID level (or none); the second picks the interface.

---

## 🔗 Related Notes

- [[Core Hardware Components]] — full concept writeup
- [[A+ Core 1 Codex]]
