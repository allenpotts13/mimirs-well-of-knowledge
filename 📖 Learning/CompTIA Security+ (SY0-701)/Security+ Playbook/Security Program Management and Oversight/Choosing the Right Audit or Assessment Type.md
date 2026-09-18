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
  - Audit Type Choice
publish: true
permalink: comptia-security-sy0-701/choosing-the-right-audit-or-assessment-type
---

# <span class="rune">ᚲ</span> Choosing the Right Audit or Assessment Type

> *How much a pen tester knows going in — everything, something, or nothing — is its own separate exam question from what kind of testing they do.*

---

## 🎯 Problem

A scenario describes an audit or a penetration test. Identify the correct type along each relevant dimension.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "a company reviews its own compliance internally" | Internal audit / self-assessment |
| "an outside regulator conducts a formal review" | External / regulatory audit |
| "an independent firm audits with no ties to the company" | Independent third-party audit |
| "a tester is given full documentation and credentials beforehand" | Known environment |
| "a tester is given no information at all" | Unknown environment |
| "a tester is given partial information" | Partially known environment |
| "testers gather information without touching the target directly" | Passive reconnaissance |
| "testers actively scan or probe the target" | Active reconnaissance |
| "both offense and defense teams collaborate during testing" | Integrated penetration test |

---

## 🧠 Why This Pattern Works

Audits and assessments are classified along independent dimensions that shouldn't be conflated: **who performs it** (internal self-assessment vs. external regulatory vs. independent third-party) is about objectivity and authority; **environment knowledge** (known/partially known/unknown) is about how much information a pen tester starts with; **reconnaissance style** (passive/active) is about whether the tester directly interacts with the target during information gathering. A scenario can specify any combination of these three independent axes.

---

## 🚨 Common Mistakes

- Assuming a self-assessment carries the same weight/objectivity as an independent third-party audit — self-assessments are useful but inherently less independent
- Confusing "unknown environment" (a pen-testing knowledge level) with "external audit" (a who-performs-it classification) — different axes entirely
- Treating passive reconnaissance as less legitimate than active — it's a deliberate, stealthier choice, not a lesser one
- Missing that a scenario can specify environment knowledge and reconnaissance style independently — they're not the same choice

---

## 💡 Wisdom from Mímir

When an audit/assessment scenario appears complex, separate it into three questions: who's doing it, how much do they know going in, and are they touching the target directly. Answer each independently.

---

## 🔗 Related Notes

- [[Audits, Assessments, and Security Awareness]] — full concept writeup
- [[Security+ Codex]]
