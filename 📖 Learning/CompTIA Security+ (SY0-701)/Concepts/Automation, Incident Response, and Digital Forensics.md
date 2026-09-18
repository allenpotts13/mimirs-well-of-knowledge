---
type: concept
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
  - Security+ Automation
  - Security+ Incident Response
  - Security+ Digital Forensics
publish: true
permalink: comptia-security-sy0-701/automation-incident-response-and-digital-forensics
---

# <span class="rune">ᛟ</span> Automation, Incident Response, and Digital Forensics

> *Incident response has seven steps in a strict order — and evidence handling has rules just as strict running alongside it.*

---

## 🎯 Purpose

Covers 4.7-4.9 of the Security Operations domain: automation/orchestration in secure operations, incident response activities, and using data sources to support an investigation (the last a "given a scenario" objective).

---

## 🧠 Key Ideas

- Automation's security value is consistency: **enforcing baselines**, **standard configurations**, and fast reaction time reduce human error at scale — but add complexity, cost, and a risk of becoming a single point of failure
- The incident response process has a strict, exam-testable order: **preparation → detection → analysis → containment → eradication → recovery → lessons learned**
- Digital forensics activities (legal hold, chain of custody, acquisition, preservation, e-discovery) run *alongside* incident response whenever evidence integrity matters for legal/HR action
- **Root cause analysis** and **threat hunting** are proactive/reflective activities distinct from the reactive incident response steps themselves
- Investigation data sources split into **logs** (firewall, application, endpoint, OS-specific, IPS/IDS, network, metadata) and **other data** (vulnerability scans, automated reports, dashboards, packet captures)

---

## ⚙️ How It Works

### Automation and orchestration

| Use case | Examples |
|---|---|
| Provisioning | User provisioning, resource provisioning, security groups |
| Operational | Ticket creation, escalation, enabling/disabling services and access, CI/testing, API integrations |
| Guard rails | Automated boundaries preventing unsafe configurations |

| Benefit | Trade-off |
|---|---|
| Efficiency/time savings, enforced baselines, standard configs, faster reaction time, workforce multiplier | Complexity, cost, single point of failure, technical debt, ongoing supportability |

### The incident response process (memorize the order)

1. **Preparation** — plans, tools, and training in place before an incident
2. **Detection** — identifying that an incident is occurring
3. **Analysis** — understanding scope and impact
4. **Containment** — limiting the damage
5. **Eradication** — removing the cause
6. **Recovery** — restoring normal operations
7. **Lessons learned** — improving for next time

Supporting activities: training, testing (tabletop exercises, simulations), root cause analysis, threat hunting.

### Digital forensics

| Concept | Purpose |
|---|---|
| Legal hold | Preserves data that may be relevant to litigation |
| Chain of custody | Documented, unbroken evidence handling trail |
| Acquisition | Collecting evidence forensically (e.g., imaging a drive) |
| Preservation | Keeping evidence unaltered |
| E-discovery | Identifying/producing electronic evidence for legal proceedings |
| Reporting | Documenting forensic findings |

### Investigation data sources

| Category | Examples |
|---|---|
| Log data | Firewall, application, endpoint, OS-specific security, IPS/IDS, network logs, metadata |
| Other data sources | Vulnerability scans, automated reports, dashboards, packet captures |

---

## 💻 Examples

```text
"A security team automatically provisions and de-provisions accounts as
employees join and leave, without manual steps."
→ Automation (user provisioning use case)

"After containing a breach, the team removes the malware and rebuilds the
affected system before restoring it to production."
→ Eradication, then recovery

"Evidence from a compromised laptop must be preserved with a documented
handling trail in case of legal action."
→ Chain of custody

"A pending lawsuit requires all related emails and files to be preserved
regardless of normal retention policy."
→ Legal hold

"An investigator correlates firewall logs, endpoint logs, and a packet
capture to reconstruct an attack timeline."
→ Using data sources to support an investigation
```

---

## 🚨 Common Mistakes

- Reordering the incident response steps — containment must happen before eradication, which must happen before recovery
- Treating "lessons learned" as optional once the incident is resolved — it's a required, final step
- Breaking chain of custody by handling evidence informally — this can invalidate evidence for legal proceedings
- Confusing root cause analysis (understanding *why* something happened) with the incident response process itself (the reactive steps taken)

---

## 📖 Further Reading

- [CompTIA Security+ (SY0-701) official exam objectives](https://www.comptia.org/certifications/security)

---

## 🔗 Related Notes

- [[Enterprise Security Capabilities and Identity Access Management]]
- [[Applying Automation and Orchestration Concepts]] — drill note
- [[Applying Incident Response Activities]] — drill note
- [[Using Data Sources to Support an Investigation]] — drill note
- [[Security+ Codex]]
