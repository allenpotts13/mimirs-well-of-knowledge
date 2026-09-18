---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA A+
difficulty: Beginner
tags:
  - a-plus-1201
  - networking
aliases:
  - Networking Hardware Choice
publish: true
permalink: comptia-a-core-1-220-1201/choosing-the-right-networking-hardware-device
---

# <span class="rune">ᚲ</span> Choosing the Right Networking Hardware Device

> *Every device in this list does exactly one job. Naming the job names the device.*

---

## 🎯 Problem

A scenario describes a networking need. Identify which physical hardware device solves it.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "connect two separate networks together" | Router |
| "connect multiple devices within the same network" | Switch |
| "need to monitor/segment traffic between switch ports" | Managed switch |
| "give wireless devices access to a wired network" | Access point |
| "centrally terminate a building's structured cabling" | Patch panel |
| "filter traffic by rule between networks" | Firewall |
| "power a device over its network cable with no nearby outlet" | PoE (injector or PoE switch) |
| "terminate a cable/fiber internet connection from the ISP" | Cable modem, DSL modem, or ONT |
| "identify a specific physical network card by its hardware address" | NIC / MAC address |

---

## 🧠 Why This Pattern Works

These devices form a clear physical stack: the ISP's line terminates at a modem/ONT, which feeds a router (routes between the ISP network and the LAN), which feeds a switch (connects devices on the LAN), which may feed access points (extend to wireless) — with a firewall filtering traffic somewhere in that chain and a patch panel just organizing the physical cabling. Once you can draw that chain, matching a device to a job becomes mechanical.

---

## 🚨 Common Mistakes

- Calling a home "router" just a router when it's usually a combo router+switch+access point+firewall device — the exam tests the individual functions separately even though consumer hardware bundles them
- Confusing a patch panel (passive, just organizes cable termination) with a switch (active, forwards traffic)
- Assuming PoE requires a special switch — a PoE injector adds power-over-Ethernet capability to a non-PoE switch's existing cable run

---

## 💡 Wisdom from Mímir

Draw the physical path traffic takes from the internet to the end device. Whatever device the scenario's problem sits *on* in that path is your answer.

---

## 🔗 Related Notes

- [[Networking Hardware and SOHO Setup]] — full concept writeup
- [[A+ Core 1 Codex]]
