---
type: concept
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Network+
difficulty: Intermediate
tags:
  - network-plus
  - network-implementation
aliases:
  - Network+ Routing
  - Network+ Switching
publish: true
permalink: comptia-network-n10-009/routing-and-switching-configuration
---

# <span class="rune">ᛟ</span> Routing and Switching Configuration

> *Routing decides which network a packet goes to. Switching decides which port it leaves through. Neither one does the other's job.*

---

## 🎯 Purpose

Covers 2.1-2.2 of the 20%-weighted Network Implementation domain: routing technology characteristics and switching technology configuration — both "given a scenario" objectives.

---

## 🧠 Key Ideas

- **Static routing** is manually configured and predictable; **dynamic routing** (BGP, EIGRP, OSPF) automatically adapts to topology changes
- Route selection is actually two separate decisions, not one ranked list: **longest prefix match** decides between *different-sized* routes that both cover a destination (always wins, regardless of source); **administrative distance** decides between *multiple sources* offering the identical prefix (lower = more trusted); **metric** is the tiebreaker only when comparing routes from the *same* protocol to that same prefix
- **NAT** translates one address to another; **PAT** (a NAT variant) lets many internal addresses share one public address via port numbers
- **VLANs** segment a switch logically without new physical wiring; **802.1Q tagging** is what lets a single trunk link carry multiple VLANs
- **Spanning Tree Protocol (STP)** exists specifically to prevent Layer 2 loops in redundant switched topologies

---

## ⚙️ How It Works

### Routing technologies

| Type | Example | Behavior |
|---|---|---|
| Static | — | Manually configured, never changes automatically |
| Dynamic — path vector | BGP | Used between autonomous systems (the internet's backbone protocol) |
| Dynamic — hybrid | EIGRP | Cisco-proprietary, fast convergence |
| Dynamic — link state | OSPF | Open standard, calculates shortest path via link-state database |

**Route selection is two separate mechanisms, not one ranked list:**
- **Longest prefix match** (forwarding decision): among routes to *different* prefixes that all cover a destination address (e.g., a /24 and a more specific /25), the most specific always wins — this applies regardless of which protocol sourced either route.
- **Administrative distance → metric** (routing table installation): when *multiple sources* advertise a route to the exact *same* prefix, the lowest administrative distance wins; if multiple routes from the *same* protocol tie on administrative distance, the lowest metric breaks the tie.

**Address translation:** NAT (one-to-one or one-to-many translation), PAT (many internal hosts share one public IP via unique port mappings — the common home-router behavior).

**FHRP (First Hop Redundancy Protocol):** provides a shared virtual IP (VIP) so if one gateway router fails, a backup takes over transparently.

### Switching technologies

| Feature | Purpose |
|---|---|
| VLAN / VLAN database | Logically segments a switch into separate broadcast domains |
| SVI (Switch Virtual Interface) | A virtual Layer 3 interface for a VLAN, enabling inter-VLAN routing |
| Native VLAN | The untagged VLAN on a trunk port |
| Voice VLAN | A dedicated VLAN for VoIP traffic, often with QoS priority |
| 802.1Q tagging | Adds a VLAN tag to frames so a trunk link can carry multiple VLANs |
| Link aggregation | Combines multiple physical links into one logical, higher-bandwidth link |
| Spanning Tree (STP) | Prevents Layer 2 loops by blocking redundant paths until needed |
| MTU / Jumbo frames | Maximum transmission unit; jumbo frames raise it above the 1500-byte default for efficiency |

---

## 💻 Examples

```text
"Two routes to the same destination exist — one from OSPF, one manually
configured statically."
→ The static route wins by default (lower administrative distance than
any dynamic protocol)

"A home router lets multiple internal devices share one public IP address
using different port numbers."
→ PAT

"A redundant pair of routers needs to fail over transparently to end
devices without any reconfiguration."
→ FHRP (shared virtual IP)

"A trunk link needs to carry traffic for VLANs 10, 20, and 30 over a
single physical connection."
→ 802.1Q tagging

"A redundant switch topology needs to prevent a broadcast storm from a
physical loop."
→ Spanning Tree Protocol
```

---

## 🚨 Common Mistakes

- Assuming the dynamic route always wins over a static one — administrative distance typically favors static routes unless configured otherwise
- Confusing NAT (translation in general) with PAT (specifically many-to-one via ports) as identical
- Forgetting a VLAN needs an SVI (or external router) to communicate with other VLANs — VLANs alone only segment, they don't route
- Disabling STP without understanding why it's there — reintroduces the exact loop risk it was preventing

---

## 📖 Further Reading

- [CompTIA Network+ (N10-009) official exam objectives](https://www.comptia.org/certifications/network)

---

## 🔗 Related Notes

- [[Wireless Configuration and Physical Installation]]
- [[Choosing a Routing Protocol and Understanding Route Selection]] — drill note
- [[Configuring Switching Technologies for a Scenario]] — drill note
- [[Network+ Codex]]
