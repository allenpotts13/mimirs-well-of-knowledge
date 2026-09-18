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
  - Network Topology Choice
publish: true
permalink: comptia-network-n10-009/choosing-the-right-network-topology
---

# <span class="rune">ᚲ</span> Choosing the Right Network Topology

> *Redundancy and cost are always opposite ends of this trade-off.*

---

## 🎯 Problem

A scenario describes a network's redundancy, scale, or design requirement. Identify the correct topology or architecture.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "every device connects to a central point" | Star/hub and spoke |
| "maximum redundancy, every node reachable multiple ways" | Mesh |
| "direct link between exactly two devices" | Point to point |
| "combination of multiple topology types" | Hybrid |
| "data center design where every leaf switch reaches every spine switch" | Spine and leaf |
| "traditional core/distribution/access layered design" | Three-tier hierarchical model |
| "smaller network merges core and distribution into one tier" | Collapsed core |
| "traffic entering/leaving the data center" | North-south traffic |
| "traffic between servers inside the data center" | East-west traffic |

---

## 🧠 Why This Pattern Works

Topology questions are fundamentally about redundancy vs. cost/complexity: star is simple and cheap but has a single point of failure at the hub; mesh is expensive and complex but has no single point of failure. Data center *architectures* (spine-and-leaf, three-tier, collapsed core) are really about scaling that same trade-off for east-west traffic patterns specific to modern server-to-server communication.

---

## 🚨 Common Mistakes

- Assuming mesh is always the "correct" answer when redundancy is mentioned — cost/complexity may make a partial mesh or hybrid more realistic
- Confusing north-south (in/out of the data center) with east-west (within it) — a very commonly tested distinction
- Treating "three-tier" and "collapsed core" as competing designs for the same size network — collapsed core is specifically a *smaller-scale* simplification of three-tier

---

## 💡 Wisdom from Mímir

If a scenario emphasizes "no single point of failure," lean mesh or spine-and-leaf. If it emphasizes simplicity or low cost, lean star or collapsed core.

---

## 🔗 Related Notes

- [[Ports, Protocols, Media, and Topologies]] — full concept writeup
- [[Network+ Codex]]
