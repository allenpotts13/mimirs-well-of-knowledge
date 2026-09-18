---
type: concept
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Security+
difficulty: Intermediate
tags:
  - security-plus
  - security-architecture
aliases:
  - Security+ Architecture Models
  - Security+ Infrastructure Security
publish: true
permalink: comptia-security-sy0-701/architecture-models-and-infrastructure-security
---

# <span class="rune">ᛟ</span> Architecture Models and Infrastructure Security

> *Fail-open or fail-closed is the one design decision in this entire domain that can't be undone after an incident starts.*

---

## 🎯 Purpose

Covers 3.1-3.2 of the 18%-weighted Security Architecture domain: security implications of different architecture models, and applying security principles to secure enterprise infrastructure — the latter a "given a scenario" objective.

---

## 🧠 Key Ideas

- Architecture decisions trade off **availability, resilience, cost, scalability, and ease of recovery** — no single model wins on every axis
- **Air-gapped** (physically isolated) and **logical segmentation** both isolate systems, but air-gapping removes network connectivity entirely while segmentation controls it
- **Fail-open** (allows traffic through on failure — prioritizes availability) vs. **fail-closed** (blocks traffic on failure — prioritizes security) is a foundational, consequential design choice for any inline security device
- **Inline vs. tap/monitor** placement determines whether a device can actively block traffic (inline) or only observe it (tap) — this single distinction separates IPS from IDS in practice, not just by name
- Secure communication relies on layered protocols: **TLS** secures a session, **IPSec** secures at the network layer, **SD-WAN/SASE** secure and optimize distributed connectivity

---

## ⚙️ How It Works

### Architecture and infrastructure concepts

| Concept | Detail |
|---|---|
| Cloud responsibility matrix | Defines what the provider vs. customer secures |
| IaC | Infrastructure defined and deployed via code |
| Serverless | No managed servers — provider handles the underlying infrastructure |
| Microservices | Application built as independent, loosely coupled services |
| Air-gapped | Physically isolated, no network connection at all |
| Logical segmentation | Network-based isolation without physical separation |
| SDN | Centralized, software-driven network control |
| ICS/SCADA, RTOS, embedded systems | Specialized environments with unique constraints (often can't be easily patched) |

**Considerations when choosing an architecture:** availability, resilience, cost, responsiveness, scalability, ease of deployment/recovery, risk transference, patch availability/inability to patch, power, and compute constraints.

### Infrastructure security principles

| Concept | Detail |
|---|---|
| Device placement / security zones | Where a device sits determines what it can see and protect |
| Attack surface | Everything an attacker could potentially target |
| Fail-open vs. fail-closed | Availability-first vs. security-first failure behavior |
| Active vs. passive | Device participates in traffic vs. observes only |
| Inline vs. tap/monitor | Can block traffic vs. can only see it |

### Network appliances

| Appliance | Role |
|---|---|
| Jump server | Hardened intermediary for administrative access |
| Proxy server | Represents/filters client requests |
| IPS/IDS | Inline blocking vs. passive detection |
| Load balancer | Distributes traffic across servers |
| WAF | Filters application-layer (web) traffic specifically |
| UTM | Combines multiple security functions in one appliance |
| NGFW | Firewall with deep packet inspection, application awareness |

### Secure communication and access

| Method | Purpose |
|---|---|
| VPN, tunneling (TLS, IPSec) | Encrypted remote connectivity |
| SD-WAN | Software-defined WAN optimizing distributed site connectivity |
| SASE | Combines networking and security delivered from the cloud edge |
| 802.1X / EAP | Port-based network access control and authentication framework |

---

## 💻 Examples

```text
"A hospital's life-support monitoring network is completely disconnected
from any other network, with no exceptions."
→ Air-gapped

"An inline firewall fails, and the design allows traffic to continue
flowing rather than halting all connectivity."
→ Fail-open (availability prioritized over security)

"A security appliance actively blocks malicious traffic in real time,
sitting directly in the traffic path."
→ Inline placement, consistent with IPS behavior

"A web application specifically needs protection against SQL injection
and XSS at the application layer."
→ WAF

"Remote branch offices need optimized, centrally managed WAN connectivity
with integrated security."
→ SD-WAN or SASE
```

---

## 🚨 Common Mistakes

- Assuming fail-closed is always the "more secure" and therefore "correct" choice — for life-safety systems, fail-open (maintaining availability) may be the deliberate, correct design
- Confusing a WAF (application-layer web traffic) with a general NGFW (broader traffic inspection with app awareness)
- Treating air-gapping and segmentation as equivalent — air-gapping removes connectivity entirely, segmentation controls it
- Placing an IDS inline expecting it to block traffic — IDS is passive by definition; only IPS blocks

---

## 📖 Further Reading

- [CompTIA Security+ (SY0-701) official exam objectives](https://www.comptia.org/certifications/security)

---

## 🔗 Related Notes

- [[Data Protection, Resilience, and Recovery]]
- [[Choosing the Right Architecture Model]] — drill note
- [[Selecting Network Security Infrastructure and Appliances]] — drill note
- [[Security+ Codex]]
