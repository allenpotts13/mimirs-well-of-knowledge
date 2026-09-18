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
  - Network+ Ports and Protocols
  - Network+ Topologies
publish: true
permalink: comptia-network-n10-009/ports-protocols-media-and-topologies
---

# <span class="rune">ᛟ</span> Ports, Protocols, Media, and Topologies

> *A 20-row port table, a wall of connector names, and five topology shapes — this note is pure memorization, no way around it.*

---

## 🎯 Purpose

Covers 1.4-1.6 of the Networking Concepts domain: common ports/protocols/traffic types, transmission media/transceivers, and network topologies/architectures.

---

## 🧠 Key Ideas

- The Network+ port list is **longer** than the A+ list — it adds SFTP, TFTP, NTP, SNMP, LDAPS, SMTPS, SQL Server, and SIP on top of the familiar HTTP/DNS/DHCP/SSH set
- Traffic types describe *how many recipients* a packet targets: unicast (one), multicast (a group), broadcast (everyone on the segment), anycast (nearest of several identical destinations)
- Transmission media splits into wireless (802.11, cellular, satellite) and wired (802.3, fiber, coax, twinaxial) — each with its own connector family
- Topologies describe the *shape* of the network; architectures like spine-and-leaf and the three-tier model describe *how data centers scale that shape*
- **North-south** traffic flows in/out of a data center; **east-west** traffic flows between devices inside it — this distinction drives spine-and-leaf design

---

## ⚙️ How It Works

### Common ports and protocols (Network+ superset)

| Port | Protocol | Port | Protocol |
|---|---|---|---|
| 20/21 | FTP | 161/162 | SNMP |
| 22 | SFTP / SSH | 389 | LDAP |
| 23 | Telnet | 443 | HTTPS |
| 25 | SMTP | 445 | SMB |
| 53 | DNS | 514 | Syslog |
| 67/68 | DHCP | 587 | SMTPS |
| 69 | TFTP | 636 | LDAPS |
| 80 | HTTP | 1433 | SQL Server |
| 123 | NTP | 3389 | RDP |
| | | 5060/5061 | SIP |

**IP types:** ICMP (diagnostics, e.g. ping), TCP, UDP, GRE (tunneling), IPSec (AH/ESP/IKE — secure tunneling).

### Traffic types

| Type | Recipients |
|---|---|
| Unicast | One specific destination |
| Multicast | A defined group |
| Broadcast | Every host on the local segment |
| Anycast | Nearest instance among several sharing an address |

### Transmission media and connectors

| Media | Connector types |
|---|---|
| Twisted pair (UTP/STP) | RJ45, RJ11 |
| Coaxial | F-type, BNC |
| Fiber (single-mode/multimode) | SC, LC, ST, MPO |
| Direct attach copper / twinaxial | Typically SFP/QSFP form factor |

**Transceiver form factors:** SFP (single lane), QSFP (quad lane, higher bandwidth). **Cable standards:** 802.3 (wired Ethernet) vs. 802.11 (wireless).

### Topologies and architectures

| Topology | Shape |
|---|---|
| Mesh | Every node connects to many/all others — high redundancy |
| Star/hub and spoke | All nodes connect to one central point |
| Point to point | Direct link between two nodes |
| Hybrid | Combination of topologies |
| Spine and leaf | Data center design — every leaf switch connects to every spine switch |

**Three-tier hierarchical model:** Core (backbone) → Distribution (policy/routing) → Access (end devices). **Collapsed core:** merges core and distribution into one tier for smaller networks. **Traffic flow:** north-south (in/out of the data center) vs. east-west (server-to-server within it).

---

## 💻 Examples

```text
"A network management system needs to poll device health via a
well-known, standardized protocol on port 161."
→ SNMP

"A video conference sends one stream to a defined group of participants
without flooding the entire network."
→ Multicast

"A data center design needs every leaf switch to reach every other leaf
in exactly two hops, minimizing east-west latency."
→ Spine and leaf

"A small office needs the simplest topology where every device connects
to one central switch."
→ Star/hub and spoke

"A fiber run needs the connector most associated with high-density,
multi-fiber trunk cabling."
→ MPO
```

---

## 🚨 Common Mistakes

- Confusing SFTP (port 22, SSH-based file transfer) with FTPS (FTP secured with TLS, different port) or plain FTP (port 20/21, unencrypted)
- Mixing up broadcast (every host on the segment) with multicast (only a defined group)
- Assuming mesh topology is always "better" — it's the most redundant but also the most expensive/complex to wire and maintain
- Forgetting east-west traffic is *internal* data center traffic, not traffic leaving the building

---

## 📖 Further Reading

- [CompTIA Network+ (N10-009) official exam objectives](https://www.comptia.org/certifications/network)

---

## 🔗 Related Notes

- [[OSI Model, Appliances, and Cloud Concepts]]
- [[IPv4 Addressing and Modern Network Architectures]]
- [[Identifying a Protocol by Port Number]] — drill note
- [[Choosing the Right Transmission Media and Connector]] — drill note
- [[Choosing the Right Network Topology]] — drill note
- [[Network+ Codex]]
