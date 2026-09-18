---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Network+
difficulty: Intermediate
tags:
  - network-plus
  - networking-concepts
  - ipv4
aliases:
  - Network+ Subnetting Drill
publish: true
permalink: comptia-network-n10-009/applying-ipv4-addressing-and-subnetting
---

# <span class="rune">ᚲ</span> Applying IPv4 Addressing and Subnetting

> *This is the one topic on the exam you actually calculate — practice the math until it's automatic.*

---

## 🎯 Problem

A scenario gives a network requirement (host count, address range, or a misbehaving address) and asks for the correct subnet, address classification, or fix.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "need X usable host addresses in a subnet" | Find the smallest CIDR block ≥ X + 2 (network + broadcast) |
| "address is 169.254.x.x" | APIPA — DHCP is unreachable |
| "address is 127.x.x.x" | Loopback |
| "address starts with 10., 172.16-31., or 192.168." | Private (RFC1918) |
| "different subnets need different sizes from the same address block" | VLSM |
| "notation like /24, /27, /30" | CIDR — directly states the subnet mask length |
| "point-to-point link needing only 2 usable addresses" | /30 (a 4-address block, 2 usable) |

---

## 🧠 Why This Pattern Works

Every subnet reserves exactly 2 addresses (network address and broadcast address) out of its block — usable hosts = block size − 2. A `/30` gives 4 addresses total, 2 usable — the standard choice for a point-to-point link. VLSM exists precisely so a single address block can be carved into differently-sized subnets instead of forcing every subnet to the same size, which would waste addresses on small links.

---

## 🚨 Common Mistakes

- Forgetting to subtract 2 (network + broadcast) when calculating usable hosts from a block size
- Assigning the same subnet size to a 2-host point-to-point link and a 200-host office LAN — that's exactly what VLSM is meant to prevent
- Treating an APIPA address as a legitimate static assignment rather than a DHCP failure symptom
- Confusing CIDR notation (`/24`) with a dotted-decimal subnet mask (255.255.255.0) as if they were different concepts — they're the same value, different notation

---

## 💡 Wisdom from Mímir

Practice converting between CIDR and usable host counts until it's reflexive — this is the one place on the exam where knowing the *concept* isn't enough; you need the arithmetic to be fast under time pressure.

---

## 🔗 Related Notes

- [[IPv4 Addressing and Modern Network Architectures]] — full concept writeup
- [[Network+ Codex]]
