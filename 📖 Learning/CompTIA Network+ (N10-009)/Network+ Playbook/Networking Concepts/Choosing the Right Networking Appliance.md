---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Network+
difficulty: Beginner
tags:
  - network-plus
  - networking-concepts
aliases:
  - Networking Appliance Choice
publish: true
permalink: comptia-network-n10-009/choosing-the-right-networking-appliance
---

# <span class="rune">ᚲ</span> Choosing the Right Networking Appliance

> *IDS watches, IPS blocks — the single letter difference the exam loves to test.*

---

## 🎯 Problem

A scenario describes a networking need. Identify the correct appliance or function.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "detect and alert on suspicious traffic without blocking it" | IDS |
| "detect and actively block suspicious traffic" | IPS |
| "distribute traffic across multiple servers" | Load balancer |
| "hide internal client identity from external destinations" | Proxy |
| "present storage as network file shares" | NAS |
| "present storage as if it were local block-level disks" | SAN |
| "prioritize voice traffic over bulk downloads" | QoS |
| "limit how many hops a packet can take before being discarded" | TTL |
| "accelerate content delivery to geographically distant users" | CDN |
| "connect wireless clients to a wired network, centrally managed" | Access point + wireless controller |

---

## 🧠 Why This Pattern Works

Appliances split into things that **move traffic** (router, switch, load balancer), things that **inspect/filter traffic** (firewall, IDS/IPS, proxy), and things that **present storage** (NAS, SAN). Functions (VPN, QoS, TTL) are behaviors layered on top of that traffic, not devices themselves. Matching the verb in the scenario ("detect," "block," "distribute," "hide," "prioritize") to the right category is the fastest path to the answer.

---

## 🚨 Common Mistakes

- Answering "IDS" when the scenario says traffic was actually blocked — that's IPS behavior
- Confusing a proxy (hides/represents clients to external destinations) with a load balancer (distributes traffic to internal servers)
- Assuming NAS and SAN are interchangeable — NAS is file-level over the network, SAN is block-level, appearing as local storage

---

## 💡 Wisdom from Mímir

If the scenario describes an *action taken* on traffic (blocked, dropped, rerouted), the answer is almost always IPS, firewall, or load balancer. If it describes *visibility only*, it's IDS or a monitoring tool.

---

## 🔗 Related Notes

- [[OSI Model, Appliances, and Cloud Concepts]] — full concept writeup
- [[Network+ Codex]]
