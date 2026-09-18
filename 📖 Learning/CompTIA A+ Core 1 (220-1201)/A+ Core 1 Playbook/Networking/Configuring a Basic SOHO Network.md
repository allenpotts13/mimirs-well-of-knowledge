---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA A+
difficulty: Beginner
tags:
  - a-plus-1201
  - networking
aliases:
  - SOHO Network Setup
publish: true
permalink: comptia-a-core-1-220-1201/configuring-a-basic-soho-network
---

# <span class="rune">ᚲ</span> Configuring a Basic SOHO Network

> *Static vs. dynamic, private vs. public, and the ever-present APIPA red flag.*

---

## 🎯 Problem

A scenario describes IP addressing behavior on a small office/home office network. Diagnose the addressing type or identify the misconfiguration.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "device shows an address starting with 169.254" | APIPA — DHCP is unreachable, this is a symptom |
| "address doesn't change and was entered manually" | Static |
| "address is assigned automatically by the network" | Dynamic (DHCP) |
| "device needs the same address every time but should still use DHCP" | DHCP reservation |
| "address like 192.168.x.x, 10.x.x.x, or 172.16-31.x.x" | Private address |
| "address is reachable directly from the internet" | Public address |
| "device can't reach anything outside its own subnet" | Missing/incorrect gateway |
| "device can reach the gateway but not other subnets correctly" | Incorrect subnet mask |

---

## 🧠 Why This Pattern Works

Every SOHO addressing scenario is really asking "where did this address come from, and does it work?" APIPA is the giveaway that DHCP failed — the device gave itself a non-routable fallback address rather than getting one from a server. A gateway problem means the device can talk *locally* but not beyond the subnet; a subnet mask problem means the device miscalculates what "local" even means.

---

## 🚨 Common Mistakes

- Treating an APIPA address as a valid, working configuration — it's evidence of a DHCP failure, not a fix
- Confusing a static IP conflict (two devices manually assigned the same address) with a DHCP scope exhaustion issue (DHCP has no addresses left to hand out) — both cause connectivity failures but need different fixes
- Assuming any 192.168.x.x address is automatically "the router" — it's just a common private range, not a fixed identity

---

## 💡 Wisdom from Mímir

If you see 169.254 anywhere in a scenario, stop analyzing the rest of the network — the answer is almost always "DHCP isn't reachable," and the real question is *why*.

---

## 🔗 Related Notes

- [[Networking Hardware and SOHO Setup]] — full concept writeup
- [[A+ Core 1 Codex]]
