---
type: concept
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Network+
difficulty: Intermediate
tags:
  - network-plus
  - network-security
aliases:
  - Network+ Attack Types
  - Network+ Defense Techniques
publish: true
permalink: comptia-network-n10-009/network-attacks-and-defense-techniques
---

# <span class="rune">ᛟ</span> Network Attacks and Defense Techniques

> *Layer 2 attacks (ARP/MAC/VLAN) get far less attention than they deserve — and this exam tests them heavily.*

---

## 🎯 Purpose

Covers 4.2-4.3 of the Network Security domain: types of network attacks and their impact, and applying network security features/defense techniques — the latter a "given a scenario" objective.

---

## 🧠 Key Ideas

- Several of the most-tested attacks here are **Layer 2** attacks (VLAN hopping, MAC flooding, ARP poisoning/spoofing) — they exploit switching behavior, not IP routing
- **DNS poisoning** corrupts a resolver's cache; **DNS spoofing** forges responses to redirect traffic — related but distinct
- **Evil twin** is a rogue AP impersonating a legitimate one; a plain **rogue device** (DHCP or AP) is unauthorized but not necessarily impersonating anything
- Defense techniques layer: **device hardening** (disable unused ports/services, change defaults), **NAC** (802.1X, port security, MAC filtering — controlling *who* connects), and **security rules** (ACLs, URL/content filtering — controlling *what* traffic is allowed)
- **Zones** (trusted/untrusted, screened subnet) physically or logically separate traffic by trust level — a screened subnet (formerly "DMZ") exposes only what must be public

---

## ⚙️ How It Works

### Attack types

| Attack | What happens |
|---|---|
| DoS / DDoS | Overwhelms a target to deny service (single source vs. distributed) |
| VLAN hopping | Attacker gains access to a VLAN they shouldn't reach |
| MAC flooding | Overwhelms a switch's MAC table, forcing it to broadcast (easier sniffing) |
| ARP poisoning/spoofing | Forges ARP replies to redirect traffic (classic on-path attack setup) |
| DNS poisoning | Corrupts a resolver's cached records |
| DNS spoofing | Forges DNS responses to redirect victims |
| Rogue DHCP/AP | Unauthorized device serving addresses or wireless access |
| Evil twin | Rogue AP specifically impersonating a legitimate, trusted SSID |
| On-path attack | Attacker intercepts traffic between two parties (formerly "man-in-the-middle") |
| Social engineering | Phishing, dumpster diving, shoulder surfing, tailgating |
| Malware | Malicious software generally |

### Defense techniques

| Category | Techniques |
|---|---|
| Device hardening | Disable unused ports/services, change default passwords |
| NAC (Network Access Control) | Port security, 802.1X, MAC filtering — controls *who* can connect |
| Security rules | ACLs, URL filtering, content filtering — controls *what* traffic passes |
| Key management | Managing encryption keys/certificates securely |
| Zones | Trusted vs. untrusted; screened subnet (isolates public-facing services) |

---

## 💻 Examples

```text
"An attacker floods a switch's MAC address table until it starts
broadcasting all traffic, making it easy to sniff."
→ MAC flooding

"An attacker sets up a wireless access point using the same SSID as the
corporate network to intercept connections."
→ Evil twin

"An attacker forges ARP replies so traffic between two hosts routes
through the attacker's machine first."
→ ARP poisoning/spoofing (enabling an on-path attack)

"A public-facing web server needs to be reachable from the internet
without exposing the internal network."
→ Screened subnet

"A switch port should only accept traffic from one specific, pre-approved
MAC address."
→ Port security / MAC filtering
```

---

## 🚨 Common Mistakes

- Confusing DNS poisoning (corrupting cached data) with DNS spoofing (forging responses in real time) — related, but not the same mechanism
- Treating any unauthorized AP as an "evil twin" — it's only an evil twin if it's impersonating a legitimate SSID; otherwise it's just a rogue AP
- Assuming NAC and ACLs solve the same problem — NAC controls *who* can join the network, ACLs control *what traffic* is permitted once connected
- Underestimating Layer 2 attacks as "less serious" than Layer 3 — VLAN hopping and ARP poisoning can fully compromise segmentation and confidentiality

---

## 📖 Further Reading

- [CompTIA Network+ (N10-009) official exam objectives](https://www.comptia.org/certifications/network)

---

## 🔗 Related Notes

- [[Network Security Fundamentals and Compliance]]
- [[Identifying Network Attack Types]] — drill note
- [[Applying Network Security Features for a Scenario]] — drill note
- [[Network+ Codex]]
