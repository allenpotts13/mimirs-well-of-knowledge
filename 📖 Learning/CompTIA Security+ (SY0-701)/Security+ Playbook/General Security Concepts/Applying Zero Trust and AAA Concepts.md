---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Security+
difficulty: Intermediate
tags:
  - security-plus
  - general-security-concepts
aliases:
  - Zero Trust Scenario Application
publish: true
permalink: comptia-security-sy0-701/applying-zero-trust-and-aaa-concepts
---

# <span class="rune">ᚲ</span> Applying Zero Trust and AAA Concepts

> *Ask "is this a decision or an enforcement?" — that single question sorts every Zero Trust component into the right plane.*

---

## 🎯 Problem

A scenario describes an access decision or Zero Trust component. Identify the correct plane, component, or AAA element.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "system evaluates policy and decides whether to grant access" | Policy Engine / Policy Administrator (control plane) |
| "the point where access is actually allowed or blocked" | Policy Enforcement Point (data plane) |
| "identity risk is continuously reassessed based on context" | Adaptive identity |
| "access is scoped to only what's needed, nothing implicit" | Threat scope reduction / least privilege |
| "confirming who a user is" | Authentication |
| "confirming what an authenticated user can do" | Authorization |
| "logging what a user actually did" | Accounting |
| "a segment of the network still trusts devices automatically" | Implicit trust zone — the thing Zero Trust aims to eliminate |

---

## 🧠 Why This Pattern Works

Zero Trust's control plane is where *decisions* get made (policy engine evaluates, policy administrator issues the decision); the data plane is where those decisions get *enforced* (the policy enforcement point actually allows/blocks the request). AAA maps cleanly onto this same decision/action distinction: authentication and authorization are decisions, accounting is the record of what happened after enforcement.

---

## 🚨 Common Mistakes

- Confusing the Policy Engine (evaluates policy) with the Policy Enforcement Point (carries out the decision) — decision vs. action
- Treating authentication and authorization as the same step — proving identity and being permitted to act are separate checks
- Assuming Zero Trust eliminates all trust — it eliminates *implicit* trust, replacing it with continuously verified, policy-driven trust
- Forgetting accounting exists even when authentication and authorization succeed — it's a record-keeping function, not a gate

---

## 💡 Wisdom from Mímir

Control plane = the brain deciding. Data plane = the hand carrying out the decision. Every Zero Trust component fits one side or the other.

---

## 🔗 Related Notes

- [[Security Controls and Fundamental Concepts]] — full concept writeup
- [[Security+ Codex]]
