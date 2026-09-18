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
aliases:
  - Routing Protocol Choice
publish: true
permalink: comptia-network-n10-009/choosing-a-routing-protocol-and-understanding-route-selection
---

# <span class="rune">ᚲ</span> Choosing a Routing Protocol and Understanding Route Selection

> *Longest prefix match always wins first — administrative distance and metric only settle ties between routes to the exact same prefix.*

---

## 🎯 Problem

A scenario describes a routing environment or a conflict between multiple available routes. Identify the correct protocol or which route wins.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "routing between different organizations/autonomous systems (the internet)" | BGP |
| "fast-converging, Cisco-proprietary" | EIGRP |
| "open-standard, link-state, calculates shortest path" | OSPF |
| "manually configured, never changes automatically" | Static routing |
| "two routes to the same destination from different sources" | Lower administrative distance wins |
| "two routes from the same protocol to the same destination" | Lower metric wins (tiebreaker) |
| "a more specific route exists alongside a less specific one" | The more specific (longer) prefix wins |
| "a gateway router needs a backup that takes over transparently" | FHRP (shared virtual IP) |

---

## 🧠 Why This Pattern Works

Route selection is two separate mechanisms, easy to conflate. **Longest prefix match** is a forwarding-time decision: when a destination address matches routes of different specificity (a /24 and a /25 both covering it), the router always forwards using the most specific one — no exceptions, regardless of source. **Administrative distance, then metric** only comes into play *before* that, when deciding which route gets installed in the table in the first place — specifically when multiple sources advertise a route to the *identical* prefix (AD picks the more trusted source; metric breaks a tie between routes from the same protocol). Protocol choice itself (BGP vs. EIGRP vs. OSPF) depends on scale and vendor: BGP for internet-scale/inter-organization routing, OSPF for open-standard enterprise routing, EIGRP where Cisco-only fast convergence is acceptable.

---

## 🚨 Common Mistakes

- Assuming the "best" or newest protocol automatically wins a route conflict — administrative distance, not protocol sophistication, decides
- Forgetting a more specific prefix (e.g., /25) always beats a less specific one (e.g., /24) regardless of administrative distance
- Confusing metric (a within-protocol cost calculation) with administrative distance (a between-protocol trust ranking) — different stages of the same decision
- Picking BGP for an internal single-organization network — massive overkill; that's OSPF/EIGRP territory

---

## 💡 Wisdom from Mímir

Ask which question the scenario is actually posing: "which of these differently-sized routes matches best?" (longest prefix match, always) or "which source do we trust for this exact route?" (administrative distance, then metric). Conflating the two is the most common way to get this wrong.

---

## 🔗 Related Notes

- [[Routing and Switching Configuration]] — full concept writeup
- [[Network+ Codex]]
