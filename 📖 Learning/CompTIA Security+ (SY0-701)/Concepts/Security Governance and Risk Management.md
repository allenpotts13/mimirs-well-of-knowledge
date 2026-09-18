---
type: concept
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Security+
difficulty: Intermediate
tags:
  - security-plus
  - security-program-management
aliases:
  - Security+ Governance
  - Security+ Risk Management
publish: true
permalink: comptia-security-sy0-701/security-governance-and-risk-management
---

# <span class="rune">ᛟ</span> Security Governance and Risk Management

> *Governance says what should happen. Risk management calculates what happens if it doesn't.*

---

## 🎯 Purpose

Covers 5.1-5.2 of the 20%-weighted Security Program Management and Oversight domain: elements of effective security governance, and the risk management process.

---

## 🧠 Key Ideas

- Governance operates through a hierarchy of documents from least to most binding: **guidelines** (suggestions) → **policies** (organizational requirements) → **procedures** (step-by-step instructions) → **standards** (specific technical requirements)
- Roles and responsibilities for data are legally distinct: **owners** (accountable for classification/protection decisions), **controllers** (decide how/why data is processed), **processors** (act on the controller's behalf), **custodians/stewards** (handle day-to-day technical management)
- Risk **appetite** (how much risk an org generally wants to take, ranging expansionary to conservative) is set at the strategic level; risk **tolerance** (acceptable variation for a specific risk) is more tactical
- Quantitative risk analysis produces a number: **SLE** (Single Loss Expectancy) × **ARO** (Annualized Rate of Occurrence) = **ALE** (Annualized Loss Expectancy) — this is the one calculation worth memorizing cold
- Risk management strategies are exactly four: **transfer** (e.g., insurance), **accept** (with exemption or formal exception), **avoid** (eliminate the activity), **mitigate** (reduce likelihood/impact)

---

## ⚙️ How It Works

### Governance document hierarchy

| Document | Nature |
|---|---|
| Guidelines | Recommended, not mandatory |
| Policies | Organizational rules (AUP, information security, BC/DR, incident response, SDLC, change management) |
| Procedures | Step-by-step instructions (change management, onboarding/offboarding, playbooks) |
| Standards | Specific mandatory technical requirements (password, access control, physical security, encryption) |

**External considerations shaping governance:** regulatory, legal, industry, local/regional/national/global requirements.

### Data roles and responsibilities

| Role | Responsibility |
|---|---|
| Owner | Accountable for the data's classification and protection |
| Controller | Decides how and why data is processed |
| Processor | Processes data on the controller's behalf |
| Custodian/Steward | Handles day-to-day technical management |

**Governance structures:** boards, committees; centralized vs. decentralized models.

### Risk management process

| Step | Activity |
|---|---|
| Identification | Discovering risks |
| Assessment | Ad hoc, recurring, one-time, or continuous evaluation |
| Analysis | Qualitative (descriptive) or quantitative (numeric) |
| Risk register | Tracks risk owners, risk threshold, ongoing status |
| Risk reporting | Communicating risk status to stakeholders |

### Quantitative risk formula

```
SLE (Single Loss Expectancy) × ARO (Annualized Rate of Occurrence) = ALE (Annualized Loss Expectancy)
```

Also relevant: probability, likelihood, impact, exposure factor.

### Risk appetite, tolerance, and strategy

| Appetite level | Meaning |
|---|---|
| Expansionary | Willing to take on significant risk for opportunity |
| Neutral | Balanced |
| Conservative | Minimizes risk-taking |

| Strategy | Action |
|---|---|
| Transfer | Shift risk elsewhere (insurance) |
| Accept | Acknowledge and proceed (exemption or exception) |
| Avoid | Eliminate the risky activity entirely |
| Mitigate | Reduce likelihood or impact |

### Business impact analysis

| Metric | Meaning |
|---|---|
| RTO | Maximum acceptable downtime |
| RPO | Maximum acceptable data loss |
| MTTR | Average time to repair |
| MTBF | Average time between failures |

---

## 💻 Examples

```text
"A specific incident is expected to cost $50,000 per occurrence and
happens about twice a year."
→ SLE = $50,000, ARO = 2, ALE = $100,000

"A company purchases cyber insurance instead of building additional
in-house defenses."
→ Risk transfer

"A company decides a risk is too costly to address right now and formally
documents accepting it with executive sign-off."
→ Risk acceptance (with exception/exemption)

"A data processor handles payroll data on behalf of a client company that
decides how that data is used."
→ The client is the controller, the payroll company is the processor

"A company sets an organization-wide willingness to take on significant
risk when pursuing new markets."
→ Expansionary risk appetite
```

---

## 🚨 Common Mistakes

- Confusing risk appetite (general strategic willingness) with risk tolerance (acceptable variation for one specific risk)
- Mixing up controller and processor roles — the controller decides *why/how*, the processor acts *on their behalf*
- Forgetting the ALE formula multiplies SLE by ARO, not adds them
- Treating "accept" as doing nothing — proper risk acceptance is a formal, documented decision (exemption or exception), not silent inaction

---

## 📖 Further Reading

- [CompTIA Security+ (SY0-701) official exam objectives](https://www.comptia.org/certifications/security)

---

## 🔗 Related Notes

- [[Third-Party Risk and Compliance]]
- [[Understanding Security Governance Elements]] — drill note
- [[Applying Risk Management Concepts]] — drill note
- [[Security+ Codex]]
