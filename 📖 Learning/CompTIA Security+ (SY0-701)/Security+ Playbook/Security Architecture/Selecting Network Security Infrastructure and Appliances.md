---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Security+
difficulty: Intermediate
tags:
  - security-plus
  - security-architecture
aliases:
  - Security Appliance Choice
publish: true
permalink: comptia-security-sy0-701/selecting-network-security-infrastructure-and-appliances
---

# <span class="rune">ᚲ</span> Selecting Network Security Infrastructure and Appliances

> *Fail-open keeps the business running. Fail-closed keeps the network safe. You can't have both when the device dies.*

---

## 🎯 Problem

A scenario describes an infrastructure security requirement. Choose the correct appliance, placement, or failure behavior.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "a security device must actively block malicious traffic, sitting in the path" | Inline placement (consistent with IPS) |
| "a device only needs to observe and alert, not block" | Tap/monitor placement (consistent with IDS) |
| "if the firewall fails, traffic must keep flowing" | Fail-open |
| "if the firewall fails, traffic must stop entirely" | Fail-closed |
| "administrators need a hardened, monitored path to manage other devices" | Jump server |
| "a web application needs protection specifically from SQL injection/XSS" | WAF |
| "one appliance should combine firewall, IPS, and content filtering" | UTM |
| "traffic needs deep packet inspection with application awareness" | NGFW |
| "traffic should be evenly distributed across multiple servers" | Load balancer |
| "network access should require 802.1X authentication before a port is usable" | Port security / 802.1X |

---

## 🧠 Why This Pattern Works

Appliance selection follows the same logic as A+/Network+: name the *function* needed (block, observe, distribute, authenticate) and match it to the device built for that function. Placement (inline vs. tap) and failure mode (fail-open vs. fail-closed) are architecture decisions layered on top of the device choice — a scenario emphasizing availability points to fail-open/tap, one emphasizing security posture points to fail-closed/inline.

---

## 🚨 Common Mistakes

- Assuming fail-closed is always correct because it "sounds more secure" — for life-safety or highly availability-dependent systems, fail-open may be the deliberate right answer
- Confusing a UTM (multiple functions in one appliance, simpler but a single point of failure) with an NGFW (a more advanced single-purpose firewall with deep inspection)
- Placing an IDS inline and expecting it to block traffic — that's IPS behavior, not IDS
- Overlooking a jump server as the answer when a scenario is really about *controlling and auditing administrative access*, not about traffic filtering

---

## 💡 Wisdom from Mímir

Ask what happens to the business if this device silently fails. If continued operation matters more than continued protection, it's fail-open. If the opposite, fail-closed.

---

## 🔗 Related Notes

- [[Architecture Models and Infrastructure Security]] — full concept writeup
- [[Security+ Codex]]
