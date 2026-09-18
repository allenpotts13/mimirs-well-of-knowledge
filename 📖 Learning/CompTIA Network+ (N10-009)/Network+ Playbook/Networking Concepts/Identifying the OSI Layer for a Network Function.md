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
  - OSI Layer Identification
publish: true
permalink: comptia-network-n10-009/identifying-the-osi-layer-for-a-network-function
---

# <span class="rune">ᚲ</span> Identifying the OSI Layer for a Network Function

> *Name the device or protocol, and the layer falls out automatically — this is the single most repeated question pattern on the exam.*

---

## 🎯 Problem

A scenario names a device, protocol, or piece of data. Identify which OSI layer it operates at.

---

## 🤔 Mental Model

| Signal in the scenario | Layer |
|---|---|
| "forwards based on MAC address" | Layer 2 (Data Link) |
| "forwards based on IP address" | Layer 3 (Network) |
| "uses port numbers, TCP or UDP" | Layer 4 (Transport) |
| "a specific application protocol (HTTP, DNS, SMTP)" | Layer 7 (Application) |
| "electrical signals, cabling, connectors" | Layer 1 (Physical) |
| "establishes/maintains a session between two apps" | Layer 5 (Session) |
| "data format, encryption, compression" | Layer 6 (Presentation) |

---

## 🧠 Why This Pattern Works

Every OSI layer has one clean identifying feature: physical (signals/cabling), data link (MAC/frames/switches), network (IP/routing), transport (ports/segments), and application (the actual protocol a user interacts with). Sessions and presentation (5-6) are rarely the star of a scenario but show up as "the odd ones out" in matching exercises — recognizing that most real-world questions cluster around layers 1, 2, 3, 4, and 7 narrows your guessing dramatically.

---

## 🚨 Common Mistakes

- Placing TCP/UDP at Layer 3 instead of Layer 4 — IP addressing is Layer 3, ports belong to Layer 4
- Forgetting switches operate at Layer 2 (unless they're Layer 3 switches, explicitly noted) while routers operate at Layer 3
- Mixing up presentation (data format/encryption) and application (the actual protocol) when a scenario mentions encryption — TLS itself spans session/presentation concepts even though HTTPS is "used" at the application layer conceptually

---

## 💡 Wisdom from Mímir

When stuck, ask "what's the addressing scheme mentioned?" MAC = Layer 2, IP = Layer 3, port = Layer 4. That question alone resolves most OSI-layer questions on this exam.

---

## 🔗 Related Notes

- [[OSI Model, Appliances, and Cloud Concepts]] — full concept writeup
- [[Network+ Codex]]
