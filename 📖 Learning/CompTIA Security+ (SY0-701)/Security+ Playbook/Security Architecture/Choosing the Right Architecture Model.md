---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Security+
difficulty: Intermediate
tags:
  - security-plus
  - security-architecture
aliases:
  - Architecture Model Choice
publish: true
permalink: comptia-security-sy0-701/choosing-the-right-architecture-model
---

# <span class="rune">ᚲ</span> Choosing the Right Architecture Model

> *Air-gapped means literally no network connection — not "a really good firewall."*

---

## 🎯 Problem

A scenario describes an infrastructure requirement. Identify the correct architecture model or design choice.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "system must have zero network connectivity, no exceptions" | Air-gapped |
| "systems separated logically but still on the network" | Logical segmentation |
| "infrastructure defined and deployed entirely through code" | IaC |
| "no servers to manage, provider runs the backend entirely" | Serverless |
| "application built as many small, independently deployable services" | Microservices |
| "industrial equipment with real-time processing requirements" | RTOS |
| "legacy factory control system, hard to patch" | ICS/SCADA |
| "centralized, software-driven network control" | SDN |
| "resources spread across more than one cloud provider" | Multi-cloud |

---

## 🧠 Why This Pattern Works

Architecture model questions test whether you recognize the specific trade-off each model makes: air-gapping trades connectivity entirely for isolation; serverless/microservices trade infrastructure control for agility and scalability; ICS/SCADA/RTOS/embedded systems trade patchability and flexibility for real-time reliability in constrained environments. The scenario's emphasis (isolation, agility, real-time reliability) points directly to the matching model.

---

## 🚨 Common Mistakes

- Calling strong network segmentation "air-gapped" — air-gapping means no connectivity whatsoever, not just strict controls
- Assuming ICS/SCADA systems can be patched like normal IT systems — many can't be, by design or due to availability requirements
- Confusing serverless (no managed servers) with microservices (an application design pattern) — they can be used together but are different concepts
- Treating multi-cloud and hybrid cloud as the same thing — multi-cloud means multiple public providers, hybrid means public + private combined

---

## 💡 Wisdom from Mímir

If the scenario says "must never touch the network," that's air-gapped — no other answer qualifies, regardless of how strong the surrounding controls are.

---

## 🔗 Related Notes

- [[Architecture Models and Infrastructure Security]] — full concept writeup
- [[Security+ Codex]]
