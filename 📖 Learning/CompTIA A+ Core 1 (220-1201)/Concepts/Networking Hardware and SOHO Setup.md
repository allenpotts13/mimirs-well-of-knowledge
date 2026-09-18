---
type: concept
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA A+
difficulty: Beginner
tags:
  - a-plus-1201
  - networking
aliases:
  - A+ SOHO Networking
  - Networking Hardware
publish: true
permalink: comptia-a-core-1-220-1201/networking-hardware-and-soho-setup
---

# <span class="rune">ᛟ</span> Networking Hardware and SOHO Setup

> *Everything in this note is what you'd actually touch wiring a small office network by hand.*

---

## 🎯 Purpose

The second half of the 23%-weighted Networking domain (2.5-2.8): physical hardware devices, IP addressing for a SOHO network, internet connection/network types, and the tools in a technician's bag. Builds on the vocabulary from [[Networking Fundamentals]].

---

## 🧠 Key Ideas

- Know each networking hardware device's *one job* — router routes between networks, switch connects devices on one network, access point bridges wired to wireless
- IPv4 addressing for SOHO covers private vs. public, static vs. dynamic, and the always-tested **APIPA** (169.254.x.x = DHCP failure)
- Internet connection types (fiber, cable, DSL, satellite, cellular, WISP) trade off speed, latency, and availability differently
- Network *types* (LAN/WAN/PAN/MAN/SAN/WLAN) are about **scope and distance**, not the technology used
- Networking tools each solve one diagnostic job — matching tool to symptom is the actual exam skill

---

## ⚙️ How It Works

### Networking hardware devices

| Device | Job |
|---|---|
| Router | Routes traffic between different networks |
| Switch (managed/unmanaged) | Connects devices within one network; managed = configurable (VLANs, monitoring) |
| Access point | Bridges wired network to wireless clients |
| Patch panel | Central termination point for structured cabling |
| Firewall | Filters traffic by rule |
| Cable modem / DSL modem / ONT | Terminates the ISP's physical connection |
| NIC | Connects a host to the network; has a MAC address |
| PoE (injector/switch) | Delivers power over Ethernet cabling |

### IPv4 addressing for SOHO networks

| Concept | Meaning |
|---|---|
| Private address | Non-routable on the internet (10.x, 172.16-31.x, 192.168.x) |
| Public address | Globally routable |
| Static | Manually assigned, doesn't change |
| Dynamic | Assigned by DHCP, can change |
| APIPA | 169.254.x.x — self-assigned when DHCP is unreachable; **symptom of a DHCP problem, not a fix** |
| Subnet mask | Defines network vs. host portion of an address |
| Gateway | The router address a host sends off-network traffic to |

### Internet connection types vs. network types

Don't confuse these two testable lists:

| Internet connection types | Network types (by scope) |
|---|---|
| Satellite | LAN — one location |
| Fiber | WAN — spans locations |
| Cable | PAN — personal, very short range (Bluetooth-scale) |
| DSL | MAN — city-scale |
| Cellular | SAN — dedicated storage network |
| WISP | WLAN — wireless LAN |

### Networking tools

| Tool | Job |
|---|---|
| Crimper | Attaches connectors (e.g. RJ45) to cable ends |
| Cable stripper | Removes cable jacket/insulation |
| Punchdown tool | Seats wire into a punchdown block/patch panel |
| Cable tester | Verifies a cable is wired correctly end-to-end |
| Toner probe | Traces a specific cable through a bundle/wall |
| Loopback plug | Tests a port's send/receive without a live network |
| Wi-Fi analyzer | Surveys wireless signal/channel congestion |
| Network tap | Passively captures traffic for inspection |

---

## 💻 Examples

```text
"A laptop shows an IP address of 169.254.10.4 and can't reach the internet."
→ APIPA — the DHCP server is unreachable, not a valid working config

"A technician needs to verify a freshly terminated Ethernet cable is wired
correctly before running it through a wall."
→ Cable tester

"An office needs internet in a location with no cable or DSL infrastructure
available at all."
→ Satellite or WISP

"Two devices in the same room need to connect directly for file transfer at
very short range."
→ PAN (e.g., Bluetooth)

"A technician needs to find where an unlabeled cable in a wiring closet
terminates."
→ Toner probe
```

---

## 🚨 Common Mistakes

- Treating an APIPA address as usable — it's diagnostic evidence, not a working IP
- Calling any switch a "router" — a switch does not route between networks
- Confusing a patch panel (passive termination) with a switch (active device)
- Mixing up MAN (metropolitan-scale network) with WAN (any multi-location network) — MAN is specifically city/campus-scale

---

## 📖 Further Reading

- [CompTIA A+ Core 1 (220-1201) official exam objectives](https://www.comptia.org/certifications/a)

---

## 🔗 Related Notes

- [[Networking Fundamentals]]
- [[Choosing the Right Networking Hardware Device]] — drill note
- [[Configuring a Basic SOHO Network]] — drill note
- [[Comparing Internet Connection Types]] — drill note
- [[A+ Core 1 Codex]]
