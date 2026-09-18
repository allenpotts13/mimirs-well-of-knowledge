---
type: concept
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Network+
difficulty: Beginner
tags:
  - network-plus
  - networking-concepts
aliases:
  - Network+ OSI Model
  - Network+ Cloud Concepts
publish: true
permalink: comptia-network-n10-009/osi-model-appliances-and-cloud-concepts
---

# <span class="rune">ᛟ</span> OSI Model, Appliances, and Cloud Concepts

> *Seven layers, one job each — everything else in this exam is a question about which layer something lives on.*

---

## 🎯 Purpose

Covers 1.1-1.3 of the 23%-weighted Networking Concepts domain: the OSI reference model, networking appliances/applications/functions, and cloud concepts/connectivity options.

---

## 🧠 Key Ideas

- The **OSI model** is the exam's organizing skeleton — nearly every device, protocol, and problem in this certification gets mentally filed by layer
- Networking appliances split into **physical/virtual devices** (router, switch, firewall, IDS/IPS, load balancer, proxy) and **functions** riding on top of them (VPN, QoS, TTL)
- Cloud concepts here are the network-specific slice of cloud computing: NFV, VPCs, security groups, cloud gateways, and connectivity options (VPN vs. Direct Connect) — not the general IaaS/PaaS/SaaS vocabulary alone
- **NAT gateways** and **internet gateways** solve different problems: NAT translates addresses for outbound access, an internet gateway is the cloud VPC's actual door to the internet

---

## ⚙️ How It Works

### The OSI model (memorize top to bottom and bottom to top)

| Layer | Name | Deals with |
|---|---|---|
| 7 | Application | End-user protocols (HTTP, DNS, SMTP) |
| 6 | Presentation | Data format/encryption/translation |
| 5 | Session | Session establishment/maintenance |
| 4 | Transport | TCP/UDP, ports, segmentation |
| 3 | Network | IP addressing, routing |
| 2 | Data Link | MAC addresses, switches, frames |
| 1 | Physical | Cables, signals, hardware |

Mnemonic: "All People Seem To Need Data Processing" (7→1).

### Networking appliances and functions

| Category | Examples |
|---|---|
| Physical/virtual appliances | Router, switch, firewall, IDS/IPS, load balancer, proxy, NAS, SAN, wireless AP/controller |
| Applications | CDN |
| Functions | VPN, QoS, TTL |

**IDS vs. IPS**: IDS detects and alerts; IPS detects and actively blocks. **NAS vs. SAN**: NAS presents file-level storage over the network; SAN presents block-level storage as if it were local disks.

### Cloud concepts for networking

| Concept | Purpose |
|---|---|
| NFV (Network Functions Virtualization) | Runs network functions (firewall, router) as software instead of dedicated hardware |
| VPC (Virtual Private Cloud) | An isolated network segment within a public cloud |
| Network security groups/lists | Cloud-native firewall rules at the instance or subnet level |
| Internet gateway | The VPC's connection point to the public internet |
| NAT gateway | Lets private-subnet resources reach the internet outbound without being directly reachable inbound |
| VPN / Direct Connect | Encrypted-over-internet vs. dedicated private circuit to the cloud |

**Cloud characteristics:** scalability (can scale), elasticity (automatically scales), multitenancy (shared underlying infrastructure, logically isolated).

---

## 💻 Examples

```text
"A switch forwards a frame based on a MAC address table."
→ Layer 2 (Data Link)

"A router forwards a packet based on the destination IP address."
→ Layer 3 (Network)

"A device passively logs suspicious traffic and alerts an admin without
blocking it."
→ IDS (not IPS, which would block)

"A cloud VPC's private-subnet servers need outbound internet access without
being directly reachable from the internet."
→ NAT gateway

"A company needs a dedicated, non-internet circuit to their cloud provider
for consistent low latency."
→ Direct Connect
```

---

## 🚨 Common Mistakes

- Confusing NAS (file-level, network share) with SAN (block-level, appears as local storage) — different presentation layer entirely
- Mixing up an internet gateway (the VPC's door out) with a NAT gateway (translates addresses for outbound-only access)
- Placing TCP/UDP at Layer 3 instead of Layer 4 — IP addressing is Layer 3, ports/segments are Layer 4
- Assuming IDS can stop an attack in progress — only IPS actively blocks

---

## 📖 Further Reading

- [CompTIA Network+ (N10-009) official exam objectives](https://www.comptia.org/certifications/network)

---

## 🔗 Related Notes

- [[Ports, Protocols, Media, and Topologies]]
- [[Identifying the OSI Layer for a Network Function]] — drill note
- [[Choosing the Right Networking Appliance]] — drill note
- [[Choosing the Right Cloud Service and Deployment Model]] — drill note
- [[Network+ Codex]]
