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
  - Internet Connection Type Choice
publish: true
permalink: comptia-a-core-1-220-1201/comparing-internet-connection-types
---

# <span class="rune">ᚲ</span> Comparing Internet Connection Types

> *Availability beats speed when there's no infrastructure at all — that's the whole trick to this question type.*

---

## 🎯 Problem

A scenario describes a location and its connectivity constraints. Choose the appropriate internet connection type.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "extremely remote location, no wired infrastructure of any kind" | Satellite |
| "fastest possible speed, low latency, available in the area" | Fiber |
| "widely available in most residential areas, decent speed" | Cable |
| "older phone-line-based infrastructure, distance from provider matters" | DSL |
| "connectivity needed for a moving vehicle or temporary site" | Cellular |
| "fixed wireless service from a local provider, no cable/DSL available" | WISP |

---

## 🧠 Why This Pattern Works

These options rank roughly by *infrastructure dependency*: fiber and cable need the most buildout but offer the best performance; DSL reuses existing phone lines but degrades with distance; satellite and cellular need no local wiring at all but trade off latency or mobility-focused pricing; WISP fills the gap for rural areas without cable/DSL but without needing a satellite dish. The scenario almost always tells you what infrastructure is or isn't available — that constraint picks the answer.

---

## 🚨 Common Mistakes

- Picking fiber or cable for a scenario that explicitly says no wired infrastructure is available — the answer has to be satellite, cellular, or WISP
- Forgetting DSL performance depends heavily on distance from the provider's central office — a "slow DSL" scenario is usually a distance issue, not a bad line
- Confusing WISP (fixed wireless from a local ISP, needs line-of-sight to a tower) with cellular (mobile carrier network, works anywhere with coverage)

---

## 💡 Wisdom from Mímir

Read for what infrastructure the scenario rules out before deciding what it implies is available — internet connection type questions are elimination questions dressed up as identification questions.

---

## 🔗 Related Notes

- [[Networking Hardware and SOHO Setup]] — full concept writeup
- [[A+ Core 1 Codex]]
