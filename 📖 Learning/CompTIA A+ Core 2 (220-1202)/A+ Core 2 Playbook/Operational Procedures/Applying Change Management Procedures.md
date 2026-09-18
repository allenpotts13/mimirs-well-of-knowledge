---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA A+
difficulty: Beginner
tags:
  - a-plus-1202
  - operational-procedures
aliases:
  - Change Management Choice
publish: true
permalink: comptia-a-core-2-220-1202/applying-change-management-procedures
---

# <span class="rune">ᚲ</span> Applying Change Management Procedures

> *"It's just a small fix" is exactly the sentence change management exists to catch.*

---

## 🎯 Problem

A scenario describes a proposed IT change. Identify the correct change type or the missing procedural step.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "routine, pre-approved, low-risk change" | Standard change |
| "requires review and approval before implementation" | Normal change |
| "urgent, security-critical, can't wait for the normal cycle" | Emergency change |
| "change is scheduled outside business hours to avoid disruption" | Maintenance window |
| "no changes allowed during a critical business period" | Change freeze |
| "proposal has no way to undo it if something breaks" | Missing rollback plan |
| "proposal has no way to recover data if something breaks" | Missing backup plan |
| "change hasn't been tested outside of production" | Missing sandbox testing |
| "the fix was applied but no one confirmed it solved the original problem" | Missing end-user acceptance |

---

## 🧠 Why This Pattern Works

Change type (standard/normal/emergency) is about *urgency and risk level*; the surrounding procedural elements (rollback plan, backup plan, sandbox testing, approval, end-user acceptance) are about *safety net and validation*, independent of which type applies. A scenario testing "what's missing" is almost always pointing at one of the safety-net elements, not the change type itself.

---

## 🚨 Common Mistakes

- Labeling every unplanned change as "emergency" — a change can be urgent without qualifying if proper process still applies
- Assuming emergency changes skip documentation entirely — they're documented after the fact, not exempted
- Forgetting sandbox testing and end-user acceptance are separate checks — one validates *before* deployment, the other validates *after*

---

## 💡 Wisdom from Mímir

Every "what went wrong" change management scenario reduces to one missing question: what happens if this fails? If the scenario doesn't answer that, that's the gap being tested.

---

## 🔗 Related Notes

- [[Documentation, Change Management, and Backup]] — full concept writeup
- [[A+ Core 2 Codex]]
