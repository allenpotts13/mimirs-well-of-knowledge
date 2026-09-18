---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Security+
difficulty: Intermediate
tags:
  - security-plus
  - security-operations
  - incident-response
aliases:
  - Incident Response Step Identification
publish: true
permalink: comptia-security-sy0-701/applying-incident-response-activities
---

# <span class="rune">ᚲ</span> Applying Incident Response Activities

> *Containment before eradication, always — you don't remove the fire before you've stopped it from spreading.*

---

## 🎯 Problem

A scenario describes an action taken during a security incident. Identify which step of the incident response process it represents, or which step is missing.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "the team has a documented plan and trained staff before anything happens" | Preparation |
| "unusual activity is first noticed" | Detection |
| "the scope and impact of the incident are being determined" | Analysis |
| "the affected system is isolated to stop the spread" | Containment |
| "malware is removed and the root cause addressed" | Eradication |
| "systems are restored to normal operation" | Recovery |
| "the team reviews what happened to improve future response" | Lessons learned |
| "a system is rebuilt before confirming full containment first" | Steps taken out of order — eradication attempted before containment |

---

## 🧠 Why This Pattern Works

The seven-step order matters because each step depends on the one before it: you can't contain what you haven't detected and analyzed, and you can't safely eradicate what isn't yet contained (or the threat can simply re-establish itself). Recovery only makes sense once the cause is actually gone. Exam scenarios frequently describe a step happening out of order specifically to test whether you'll catch the sequencing error.

---

## 🚨 Common Mistakes

- Skipping straight to eradication/recovery without confirming containment first
- Treating "lessons learned" as optional once systems are back online — it's a required final step
- Confusing detection (noticing something happened) with analysis (understanding its scope and impact) — different steps
- Forgetting preparation is itself a step in the process, not just "everything that happens before an incident"

---

## 💡 Wisdom from Mímir

Recite the order until it's automatic: preparation, detection, analysis, containment, eradication, recovery, lessons learned. Most incident response questions are really asking "what comes next" or "what was skipped."

---

## 🔗 Related Notes

- [[Automation, Incident Response, and Digital Forensics]] — full concept writeup
- [[Security+ Codex]]
