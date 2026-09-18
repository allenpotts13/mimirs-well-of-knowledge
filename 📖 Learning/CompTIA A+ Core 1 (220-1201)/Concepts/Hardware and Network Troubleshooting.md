---
type: concept
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA A+
difficulty: Intermediate
tags:
  - a-plus-1201
  - troubleshooting
aliases:
  - A+ Troubleshooting Methodology
publish: true
permalink: comptia-a-core-1-220-1201/hardware-and-network-troubleshooting
---

# <span class="rune">ᛟ</span> Hardware and Network Troubleshooting

> *The single biggest domain on Core 1 — 28% — and every one of its six sub-objectives is "given a scenario." This is where the exam actually tests judgment, not recall.*

---

## 🎯 Purpose

28% of the exam, the largest domain by weight. Covers the official CompTIA troubleshooting methodology plus six symptom-diagnosis areas: motherboard/RAM/CPU/power, drives/RAID, video/display, mobile devices, network, and printers. Each area has its own dedicated Playbook drill note — this note covers the shared methodology and a quick-reference map of symptoms to areas.

---

## 🧠 Key Ideas

- CompTIA defines one troubleshooting methodology used across *every* domain, not just hardware — memorize the six steps in order
- "Establish a theory of probable cause" explicitly means **question the obvious first** — check the simple explanation before an exotic one
- Every troubleshooting scenario ends the same way: verify full functionality, then document — skipping documentation is a real exam trap
- The six symptom areas below each have a predictable, memorizable list of "common symptoms" the exam draws from almost verbatim
- Recognizing *which area* a symptom belongs to is half the battle — a "grinding noise" means a drive, not a fan; a "burning smell" means power/motherboard, not a display

---

## ⚙️ How It Works

### The CompTIA troubleshooting methodology

1. **Identify the problem.**
2. **Establish a theory of probable cause.** (Question the obvious — research knowledge base/internet if applicable.)
3. **Test the theory** to determine the cause.
4. **Establish a plan of action** to resolve the problem, then implement the solution.
5. **Verify full system functionality** and implement preventive measures if applicable.
6. **Document findings, actions, and outcomes.**

> [!tip] The order is the test
> Exam questions often describe a technician skipping a step (e.g., replacing a part before testing a theory, or fixing the issue without documenting). Spotting the *skipped* step is a common question format.

### The six troubleshooting areas at a glance

| Area | Signature symptoms |
|---|---|
| Motherboard/RAM/CPU/power | POST beeps, no power, random shutdown, overheating, burning smell, capacitor swelling |
| Drive/RAID | Grinding/clicking noise, S.M.A.R.T. failure, bootable device not found, array missing |
| Video/display | Dim/fuzzy/distorted image, dead pixels, burn-in, incorrect input source |
| Mobile devices | Swollen battery, liquid damage, digitizer issues, degraded performance |
| Network | Intermittent connectivity, high latency, jitter, port flapping, authentication failures |
| Printers | Paper jams, garbled print, faded prints, frozen print queue |

Full symptom-by-symptom drill-downs live in the Playbook — see Related Notes below.

---

## 💻 Examples

```text
"A technician replaces a component without first confirming what's actually
causing the symptom."
→ Skipped step 2/3 — no theory established or tested

"After a repair, the technician moves on without checking whether the
original issue (and only that issue) is resolved."
→ Skipped step 5 — verify full system functionality

"A PC won't POST and beeps in a repeating pattern."
→ Motherboard/RAM/CPU/power area — check the beep code

"A drive makes a clicking sound and files are intermittently missing."
→ Drive/RAID area — likely imminent mechanical failure
```

---

## 🚨 Common Mistakes

- Jumping straight to "replace the part" without establishing and testing a theory first
- Treating "question the obvious" as optional — most real (and tested) failures have mundane causes
- Skipping documentation because the problem is already fixed — it's a required step, not an afterthought
- Misclassifying a symptom's area — e.g., assuming all "no display" issues are the monitor when it could be the video cable, GPU, or motherboard

---

## 📖 Further Reading

- [CompTIA A+ Core 1 (220-1201) official exam objectives](https://www.comptia.org/certifications/a)

---

## 🔗 Related Notes

- [[Troubleshooting Motherboard, RAM, CPU, and Power Issues]] — drill note
- [[Troubleshooting Drive and RAID Issues]] — drill note
- [[Troubleshooting Video, Projector, and Display Issues]] — drill note
- [[Troubleshooting Common Mobile Device Issues]] — drill note
- [[Troubleshooting Network Issues]] — drill note
- [[Troubleshooting Printer Issues]] — drill note
- [[A+ Core 1 Codex]]
