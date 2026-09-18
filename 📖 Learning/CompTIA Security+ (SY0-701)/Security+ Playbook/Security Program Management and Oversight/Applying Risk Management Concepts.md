---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Security+
difficulty: Intermediate
tags:
  - security-plus
  - security-program-management
aliases:
  - Risk Calculation and Strategy Choice
publish: true
permalink: comptia-security-sy0-701/applying-risk-management-concepts
---

# <span class="rune">ᚲ</span> Applying Risk Management Concepts

> *SLE × ARO = ALE is the one formula on this entire exam you should be able to do in your head under pressure.*

---

## 🎯 Problem

A scenario gives risk figures or describes a risk decision. Calculate the correct value or identify the correct risk management strategy.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "cost per incident × how often it happens per year" | SLE × ARO = ALE |
| "an organization buys insurance instead of building more defenses" | Risk transfer |
| "an organization formally documents accepting a risk with sign-off" | Risk acceptance (exemption/exception) |
| "an organization stops offering a risky service entirely" | Risk avoidance |
| "an organization adds a control to reduce likelihood or impact" | Risk mitigation |
| "leadership sets a broad appetite for taking on risk in new ventures" | Risk appetite (expansionary) |
| "a specific risk has a defined acceptable variance before action is needed" | Risk tolerance / risk threshold |
| "maximum acceptable downtime for a system after a disaster" | RTO |
| "maximum acceptable data loss measured in time" | RPO |

---

## 🧠 Why This Pattern Works

Risk management has exactly four response strategies (transfer, accept, avoid, mitigate) — every scenario resolves to one of these four verbs. The quantitative risk formula (SLE × ARO = ALE) is a straightforward multiplication once you identify which number in the scenario is the per-incident cost (SLE) and which is the frequency (ARO). Risk appetite and tolerance operate at different altitudes: appetite is the organization's general strategic posture, tolerance is the acceptable variance for one specific risk.

---

## 🚨 Common Mistakes

- Adding SLE and ARO instead of multiplying them — the formula is strictly multiplicative
- Confusing risk appetite (strategic, general) with risk tolerance (tactical, specific to one risk)
- Treating "acceptance" as passive inaction — proper risk acceptance is a formal, documented decision
- Mixing up RTO (time to restore service) with RPO (acceptable data loss) when a scenario gives a number and a time unit

---

## 💡 Wisdom from Mímir

When you see a dollar figure and a frequency in the same scenario, that's an ALE calculation waiting to happen — multiply, don't overthink it.

---

## 🔗 Related Notes

- [[Security Governance and Risk Management]] — full concept writeup
- [[Security+ Codex]]
