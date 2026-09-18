---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Network+
difficulty: Intermediate
tags:
  - network-plus
  - network-implementation
  - wireless
aliases:
  - Wireless Device Configuration Choice
publish: true
permalink: comptia-network-n10-009/configuring-wireless-devices-for-a-scenario
---

# <span class="rune">ᚲ</span> Configuring Wireless Devices for a Scenario

> *PSK for convenience, Enterprise for accountability — that's the whole authentication decision in one sentence.*

---

## 🎯 Problem

A scenario describes a wireless deployment requirement. Choose the correct configuration.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "every employee needs individual, revocable wireless credentials" | Enterprise authentication (802.1X) |
| "small office, one shared password is acceptable" | PSK |
| "visitors need internet without touching the internal network" | Guest network with captive portal |
| "seamless roaming across many APs under one network name" | ESSID spanning multiple APs |
| "dense deployment needs the least congested spectrum" | 6GHz |
| "wireless bridge between two buildings" | Point-to-point wireless |
| "coverage needs to self-heal if one AP fails" | Mesh network |
| "many APs need central configuration and policy" | Lightweight APs + wireless controller |

---

## 🧠 Why This Pattern Works

Wireless scenarios test layered decisions: network type (infrastructure/mesh/ad hoc/point-to-point) answers "how are devices physically/logically connected," authentication (PSK/Enterprise) answers "how are users verified," and management model (autonomous/lightweight+controller) answers "how is configuration maintained at scale." A scenario mentioning individual accountability or centralized policy is signaling Enterprise + controller-managed APs; a scenario mentioning simplicity or a small office is signaling PSK + autonomous APs.

---

## 🚨 Common Mistakes

- Choosing PSK for a scenario that explicitly needs per-user revocation or auditing — only Enterprise authentication provides that
- Assuming a guest network with a captive portal is fully isolated by default — isolation has to be explicitly configured, not just assumed from having a portal
- Picking mesh when the scenario actually describes a simple two-building bridge — that's point-to-point, not mesh
- Forgetting large-scale AP deployments need a controller for practical management — autonomous APs configured individually don't scale

---

## 💡 Wisdom from Mímir

If the scenario cares who specifically connected, it wants Enterprise authentication. If it only cares that someone with the password connected, PSK is enough.

---

## 🔗 Related Notes

- [[Wireless Configuration and Physical Installation]] — full concept writeup
- [[Network+ Codex]]
