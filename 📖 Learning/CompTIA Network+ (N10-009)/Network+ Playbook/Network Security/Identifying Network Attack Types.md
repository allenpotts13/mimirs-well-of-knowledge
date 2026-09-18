---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Network+
difficulty: Intermediate
tags:
  - network-plus
  - network-security
aliases:
  - Network Attack Identification
publish: true
permalink: comptia-network-n10-009/identifying-network-attack-types
---

# <span class="rune">ᚲ</span> Identifying Network Attack Types

> *Layer 2 attacks (ARP, MAC, VLAN) get underrated — they're some of the most-tested items in this whole domain.*

---

## 🎯 Problem

A scenario describes malicious network activity. Identify the specific attack type.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "flood a target to make it unavailable, single source" | DoS |
| "flood a target from many distributed sources" | DDoS |
| "attacker gains access to a VLAN they shouldn't reach" | VLAN hopping |
| "attacker floods a switch's MAC table to force broadcast behavior" | MAC flooding |
| "attacker forges ARP replies to redirect local traffic" | ARP poisoning/spoofing |
| "attacker corrupts a DNS resolver's cache" | DNS poisoning |
| "attacker forges DNS responses in real time" | DNS spoofing |
| "unauthorized AP or DHCP server appears on the network" | Rogue device |
| "unauthorized AP impersonates a legitimate SSID" | Evil twin |
| "attacker intercepts traffic between two communicating parties" | On-path attack |
| "attacker searches discarded documents for sensitive data" | Dumpster diving |
| "attacker follows an employee through a secured door" | Tailgating |

---

## 🧠 Why This Pattern Works

Group these by the layer/vector they exploit: **Layer 2** (VLAN hopping, MAC flooding, ARP poisoning/spoofing) manipulate switching behavior directly; **DNS-based** (poisoning, spoofing) redirect victims by corrupting name resolution; **wireless-specific** (evil twin, rogue AP) exploit trust in a familiar network name; **social engineering** (phishing, dumpster diving, shoulder surfing, tailgating) exploit people, not protocols. Identifying which category a scenario belongs to narrows the specific attack name quickly.

---

## 🚨 Common Mistakes

- Labeling every rogue AP an "evil twin" — it's only an evil twin if it's specifically impersonating a legitimate, trusted SSID
- Confusing DNS poisoning (corrupting cached records over time) with DNS spoofing (forging a response in the moment) — related but distinct mechanisms
- Missing that ARP poisoning is usually the *setup* for an on-path attack, not the end goal itself
- Treating DDoS as "just a bigger DoS" without recognizing the distributed-source aspect is what makes it harder to block with simple IP filtering

---

## 💡 Wisdom from Mímir

When an attack scenario doesn't clearly name a technical mechanism, check whether it's exploiting a person instead of a protocol — that's the fastest way into the social engineering category.

---

## 🔗 Related Notes

- [[Network Attacks and Defense Techniques]] — full concept writeup
- [[Network+ Codex]]
