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
  - Backup Strategy Choice
publish: true
permalink: comptia-a-core-2-220-1202/choosing-the-right-backup-and-recovery-method
---

# <span class="rune">ᚲ</span> Choosing the Right Backup and Recovery Method

> *Fast backups mean slow restores, and fast restores mean slow backups — every choice here is that trade-off.*

---

## 🎯 Problem

A scenario describes a backup or recovery requirement. Choose the correct backup type, rotation scheme, or recovery method.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "backup window must be as short as possible every night" | Incremental |
| "restore speed matters more than backup time, some flexibility on backup time" | Differential |
| "need a complete, self-contained copy regardless of time cost" | Full |
| "combine full + increments into a new full without re-reading source data" | Synthetic full |
| "protect against a fire or theft at the primary site" | Offsite copy |
| "rotation scheme with monthly/weekly/daily tiers" | Grandfather-Father-Son (GFS) |
| "backup strategy must meet an industry-standard minimum" | 3-2-1 rule (3 copies, 2 media types, 1 offsite) |
| "restore directly onto the original failed system" | In-place/overwrite recovery |
| "restore onto different hardware entirely" | Alternative location recovery |
| "backups have never actually been verified to work" | Missing backup testing |

---

## 🧠 Why This Pattern Works

Backup type selection is a direct trade-off along one axis: full backups are slow to create but fast to restore; incremental backups are fast to create but slow to restore (every increment since the last full must be replayed in order); differential sits in between. Rotation schemes and the 3-2-1 rule are a separate axis entirely — they're about *how many copies exist and where*, not how each individual backup captures data.

---

## 🚨 Common Mistakes

- Choosing incremental for a scenario that prioritizes fast restores — that's differential or full, not incremental
- Treating a backup as "done" without ever testing an actual restore
- Satisfying "3-2-1" with 3 copies on the same media type or all onsite — both the media-type and offsite requirements must be met, not just the copy count

---

## 💡 Wisdom from Mímir

When asked to pick a backup type, ask which side of the trade-off the scenario actually cares about — backup window or restore window — and pick accordingly; almost every wrong answer optimizes the side that wasn't asked for.

---

## 🔗 Related Notes

- [[Documentation, Change Management, and Backup]] — full concept writeup
- [[A+ Core 2 Codex]]
