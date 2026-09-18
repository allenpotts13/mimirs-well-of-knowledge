---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Network+
difficulty: Beginner
tags:
  - network-plus
  - network-implementation
aliases:
  - Physical Installation Choice
publish: true
permalink: comptia-network-n10-009/planning-a-physical-network-installation
---

# <span class="rune">ᚲ</span> Planning a Physical Network Installation

> *Half of physical installation questions are really about power and airflow, not cabling.*

---

## 🎯 Problem

A scenario describes a physical networking space or equipment need. Identify the correct planning consideration.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "equipment must survive a brief power outage" | UPS |
| "power needs to be distributed to multiple rack devices" | PDU |
| "small satellite wiring closet feeding a floor" | IDF |
| "the building's central cabling termination point" | MDF |
| "prevent unauthorized physical access to equipment" | Lockable rack/enclosure |
| "airflow must pull cool air in front, exhaust hot air back" | Port-side exhaust/intake planning |
| "server room needs automatic fire response without damaging equipment" | Fire suppression (non-water-based) |
| "humidity/temperature swings threaten equipment reliability" | Environmental controls |
| "fiber terminations need organized management" | Fiber distribution panel |

---

## 🧠 Why This Pattern Works

Physical installation splits into four concerns: **space** (rack size, IDF/MDF placement, airflow direction), **cabling** (patch panels, fiber distribution, lockable access), **power** (UPS for outages, PDU for distribution, voltage/load planning), and **environment** (temperature, humidity, fire suppression). A scenario naming a specific symptom (equipment overheating, power loss, unauthorized access) maps directly to exactly one of these four categories.

---

## 🚨 Common Mistakes

- Confusing a UPS (keeps equipment running through an outage) with a PDU (just distributes power within a rack, no battery backup)
- Assuming any fire suppression system is appropriate for a server room — water-based systems can destroy the very equipment they're meant to protect
- Overlooking airflow direction (port-side exhaust/intake) as "not really networking" — mismatched airflow direction across adjacent racks causes real overheating
- Treating IDF and MDF as interchangeable — MDF is the main/central point, IDFs are satellite closets feeding out from it

---

## 💡 Wisdom from Mímir

When a physical installation scenario doesn't mention cabling at all, check power and environment first — those two categories account for most of the "not actually about cables" trick questions.

---

## 🔗 Related Notes

- [[Wireless Configuration and Physical Installation]] — full concept writeup
- [[Network+ Codex]]
