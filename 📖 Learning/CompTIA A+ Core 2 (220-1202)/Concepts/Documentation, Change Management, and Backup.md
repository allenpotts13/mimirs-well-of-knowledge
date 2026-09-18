---
type: concept
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA A+
difficulty: Beginner
tags:
  - a-plus-1202
  - operational-procedures
aliases:
  - A+ Documentation and Change Management
  - A+ Backup and Recovery
publish: true
permalink: comptia-a-core-2-220-1202/documentation-change-management-and-backup
---

# <span class="rune">ᛟ</span> Documentation, Change Management, and Backup

> *None of this is glamorous. All of it is what separates a professional from someone who just fixes things.*

---

## 🎯 Purpose

Covers 4.1-4.3 of the 21%-weighted Operational Procedures domain: documentation/ticketing/asset management practices, change management procedures, and backup/recovery methods.

---

## 🧠 Key Ideas

- A ticketing system captures user info, device info, issue description, category, severity, and escalation level — structured for handoff, not just a note to yourself
- **Asset management** (inventory, CMDB, tags/IDs, procurement lifecycle, warranty/licensing) is how an org tracks what it owns and who's using it
- Change management exists to prevent well-intentioned fixes from causing new outages — every change needs a **rollback plan** and a **backup plan** before implementation
- Backup **type** (full/incremental/differential/synthetic full) trades off backup time against restore time and storage use
- The **3-2-1 backup rule** (3 copies, 2 different media types, 1 offsite) is the baseline standard to know cold

---

## ⚙️ How It Works

### Documentation and support systems

| Element | Purpose |
|---|---|
| Ticketing system | User/device info, issue description, category, severity, escalation |
| Asset management | Inventory lists, CMDB, asset tags/IDs, procurement lifecycle, warranty/licensing, assigned users |
| SOPs | Standard operating procedures — software install steps, onboarding/off-boarding checklists |
| SLAs | Internal or external/third-party service commitments |
| Knowledge base | Searchable articles for known issues/resolutions |

### Change management

| Element | Purpose |
|---|---|
| Change types | Standard (pre-approved, low-risk), Normal (needs approval), Emergency (urgent, approved after the fact if needed) |
| Request form | Documents purpose, scope, affected systems |
| Risk analysis | Risk level assessment before approval |
| Rollback plan | How to undo the change if it fails |
| Backup plan | Data safety net before the change happens |
| Sandbox testing | Test the change in isolation first |
| Change board approval | Formal sign-off before implementation |
| Change freeze / maintenance window | Designated times changes are/aren't allowed |
| End-user acceptance | Confirms the change met the actual need |

### Backup types

| Type | What it captures | Restore speed | Backup speed |
|---|---|---|---|
| Full | Everything | Fastest | Slowest |
| Incremental | Changes since last backup (any type) | Slowest (needs full + every increment) | Fastest |
| Differential | Changes since last *full* backup | Faster than incremental | Faster than full |
| Synthetic full | Combines prior full + increments into a new "full" without re-reading source | Fast | Efficient, offloads work from production |

### Backup rotation and rules

- **Onsite vs. offsite**: offsite protects against site-level disasters (fire, theft)
- **Grandfather-Father-Son (GFS)**: a rotation scheme with monthly (grandfather), weekly (father), and daily (son) backup tiers
- **3-2-1 rule**: 3 copies of data, on 2 different media types, with 1 copy offsite
- **Backup testing**: a backup is unverified until a restore has actually been tested

---

## 💻 Examples

```text
"A critical patch needs to go out immediately due to an active
vulnerability, bypassing the normal approval cycle."
→ Emergency change

"A backup strategy needs to minimize nightly backup time even though
restores may take longer."
→ Incremental backups

"A backup strategy needs faster restores than incremental, accepting
somewhat larger nightly backups."
→ Differential backups

"A company wants backups safe from a fire that destroys the server room."
→ Offsite copy (part of the 3-2-1 rule)

"A technician proposes a fix but has no plan for what happens if it fails
in production."
→ Missing rollback plan — required before change approval
```

---

## 🚨 Common Mistakes

- Skipping the rollback/backup plan because a change "should" work — change management exists specifically for when it doesn't
- Confusing incremental (changes since the *last backup of any kind*) with differential (changes since the *last full backup*)
- Treating a backup as valid without ever testing a restore from it
- Assuming "emergency change" means "skip documentation" — it still gets documented, just after the fact

---

## 📖 Further Reading

- [CompTIA A+ Core 2 (220-1202) official exam objectives](https://www.comptia.org/certifications/a)

---

## 🔗 Related Notes

- [[Safety, Compliance, Professionalism, and Emerging Technology]]
- [[Applying Change Management Procedures]] — drill note
- [[Choosing the Right Backup and Recovery Method]] — drill note
- [[A+ Core 2 Codex]]
