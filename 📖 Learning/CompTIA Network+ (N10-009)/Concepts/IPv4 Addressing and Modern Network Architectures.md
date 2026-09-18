---
type: concept
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
  - Network+ IPv4 Addressing
  - Network+ SDN and Zero Trust
publish: true
permalink: comptia-network-n10-009/ipv4-addressing-and-modern-network-architectures
---

# <span class="rune">ᛟ</span> IPv4 Addressing and Modern Network Architectures

> *Subnetting is the one skill on this exam you have to actually calculate, not just memorize.*

---

## 🎯 Purpose

Covers 1.7-1.8 of the Networking Concepts domain: IPv4 addressing/subnetting (a "given a scenario" objective — see the paired drill note), and evolving/modern network architecture concepts (SDN, VXLAN, zero trust, IaC, IPv6 transition).

---

## 🧠 Key Ideas

- IPv4 addresses split into **public** (routable) and **private** (RFC1918 ranges), plus special-purpose ranges: APIPA (169.254.x.x) and loopback (127.x.x.x)
- **CIDR notation** (`/24`, etc.) and **VLSM** both exist to use address space efficiently instead of rigid class-based allocation
- **SDN** separates the control plane (decision-making) from the data plane (forwarding) — this is what makes centralized, software-driven network policy possible
- **Zero trust architecture** assumes no implicit trust anywhere on the network — every request is authenticated and authorized regardless of location
- IPv6 transition techniques (dual stack, tunneling, NAT64) exist because IPv4 and IPv6 don't natively interoperate

---

## ⚙️ How It Works

### IPv4 address classes and private ranges

| Class | Range | Use |
|---|---|---|
| A | 1-126 | Very large networks |
| B | 128-191 | Medium networks |
| C | 192-223 | Small networks |
| D | 224-239 | Multicast |
| E | 240-255 | Experimental |

| Special range | Purpose |
|---|---|
| RFC1918 private | 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 |
| APIPA | 169.254.0.0/16 — self-assigned, DHCP unreachable |
| Loopback | 127.0.0.0/8 — localhost |

### CIDR and VLSM

CIDR notation (e.g., `/24`) expresses subnet mask length directly, replacing rigid class-based boundaries. **VLSM** lets a network use different subnet sizes for different segments instead of one fixed size everywhere — critical for not wasting address space on small point-to-point links while still supporting large user subnets.

### Modern network architecture concepts

| Concept | What it does |
|---|---|
| SDN / SD-WAN | Centralizes and automates network control via software, separate from the physical forwarding hardware |
| VXLAN | Layer 2 encapsulation over a Layer 3 network — extends broadcast domains across data centers (DCI) |
| Zero trust architecture (ZTA) | No implicit trust; policy-based authentication, authorization, least privilege everywhere |
| SASE / SSE | Combines networking and security functions delivered from the cloud edge |
| Infrastructure as Code (IaC) | Network configuration defined in version-controlled files, applied via automation (playbooks, templates) |

### IPv6 transition and address exhaustion

| Technique | Purpose |
|---|---|
| Dual stack | Run IPv4 and IPv6 simultaneously |
| Tunneling | Encapsulate IPv6 traffic inside IPv4 (or vice versa) to cross incompatible segments |
| NAT64 | Translates between IPv6-only and IPv4-only networks |

---

## 💻 Examples

```text
"A network needs one subnet sized for 500 hosts and another for a 2-host
point-to-point link, without wasting address space."
→ VLSM

"A network engineer wants to push a single policy change across hundreds
of switches from one central controller."
→ SDN

"A remote worker's laptop, regardless of network location, must
authenticate and be authorized for every resource request."
→ Zero trust architecture

"Two data centers need their Layer 2 broadcast domains to appear
extended across a Layer 3 WAN link."
→ VXLAN

"An organization needs new switch configurations to be version-controlled
and deployed via automation instead of manual CLI entry."
→ Infrastructure as Code
```

---

## 🚨 Common Mistakes

- Treating an APIPA address as usable — like in A+, it's a DHCP failure symptom, not a working config
- Assuming VLSM and CIDR are two names for the same thing — CIDR is the notation, VLSM is the technique of using variable subnet sizes across a network
- Confusing zero trust with a firewall or VPN — it's an overarching *philosophy* (verify everything, everywhere), not one specific tool
- Mixing up dual stack (running both protocols) with NAT64 (translating between them) — different transition strategies for different situations

---

## 📖 Further Reading

- [CompTIA Network+ (N10-009) official exam objectives](https://www.comptia.org/certifications/network)

---

## 🔗 Related Notes

- [[Ports, Protocols, Media, and Topologies]]
- [[Applying IPv4 Addressing and Subnetting]] — drill note
- [[Network+ Codex]]
