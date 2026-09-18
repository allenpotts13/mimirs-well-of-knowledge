---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Security+
difficulty: Intermediate
tags:
  - security-plus
  - security-architecture
aliases:
  - Data Protection Method Choice
publish: true
permalink: comptia-security-sy0-701/applying-data-protection-strategies
---

# <span class="rune">ᚲ</span> Applying Data Protection Strategies

> *Name the state the data is in before naming how to protect it — "at rest," "in transit," and "in use" each need a different answer.*

---

## 🎯 Problem

A scenario describes data that needs protection. Identify the correct data state, classification, or protection method.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "data stored on a laptop's disk" | Data at rest |
| "data moving between a client and a server" | Data in transit |
| "data actively being processed in a running application" | Data in use |
| "sensitive fields shown in a test environment need to look realistic but not be real" | Data masking |
| "credit card numbers are replaced with a non-sensitive substitute value" | Tokenization |
| "data must legally remain within a specific country's borders" | Data sovereignty / geographic restriction |
| "information that would damage the company if a competitor obtained it" | Trade secret |
| "information a company must protect due to law (health, financial, PII)" | Regulated data |

---

## 🧠 Why This Pattern Works

Data protection scenarios test two independent things: the **state** the data is in (at rest, in transit, in use — determining which technical control applies) and the **classification** of the data itself (sensitivity level and type — determining how strict the protection must be). A scenario naming "on disk," "over the network," or "in memory" is signaling state; a scenario naming a specific data type (health records, trade secrets, PII) is signaling classification.

---

## 🚨 Common Mistakes

- Applying an "in transit" control (like TLS) to an "at rest" problem (unencrypted disk) — they require different technical solutions
- Confusing data masking (obscures but often stays reversible/format-preserving) with tokenization (substitutes with a separate, often reversible-via-lookup token)
- Treating all sensitive data the same regardless of legal classification — regulated data (health, financial, PII) often carries specific legal handling requirements beyond general sensitivity
- Overlooking data sovereignty as a legal constraint that can override an otherwise-sound technical architecture choice

---

## 💡 Wisdom from Mímir

Always answer "where does this data live right now — at rest, in transit, or in use?" before recommending a control. The wrong answer to that question invalidates an otherwise correct-sounding technique.

---

## 🔗 Related Notes

- [[Data Protection, Resilience, and Recovery]] — full concept writeup
- [[Security+ Codex]]
