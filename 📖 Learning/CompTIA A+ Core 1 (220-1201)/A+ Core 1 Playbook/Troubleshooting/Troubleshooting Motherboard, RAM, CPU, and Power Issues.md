---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA A+
difficulty: Intermediate
tags:
  - a-plus-1201
  - troubleshooting
aliases:
  - Troubleshooting POST Failures
publish: true
permalink: comptia-a-core-1-220-1201/troubleshooting-motherboard-ram-cpu-and-power-issues
---

# <span class="rune">ᚲ</span> Troubleshooting Motherboard, RAM, CPU, and Power Issues

> *POST beeps and burning smells are the two symptoms this exam trusts you to never ignore.*

---

## 🎯 Problem

A scenario describes a system that won't boot, behaves erratically, or shows physical warning signs. Diagnose whether the root cause is the motherboard, RAM, CPU, or power system.

---

## 🤔 Mental Model

| Symptom | Likely cause |
|---|---|
| POST beeps, proprietary crash screen | Motherboard/BIOS detecting a hardware fault at boot |
| No power at all | PSU, power cable, or wall outlet — check the simplest layer first |
| Blank screen (but system powers on) | Could still be RAM/GPU/motherboard, not necessarily the display |
| Random shutdown, sluggish performance, overheating | Thermal issue — check cooling, dust, thermal paste |
| Burning smell, capacitor swelling | Failing/failed component — stop use immediately, this is a safety issue |
| Unusual noise (not a drive) | Fan bearing failure or debris |
| Inaccurate system date/time | CMOS battery is dying |
| Application crashes tied to hardware, not software | Suspect RAM — run a memory diagnostic |

---

## 🧠 Why This Pattern Works

Apply the methodology: **identify** the symptom category first (won't power on at all vs. powers on but fails later vs. runs but is unstable), then use POST beep codes and BIOS diagnostics to **narrow the theory** before opening the case. A dying CMOS battery causing wrong system time is a classic "looks unrelated but has one specific cause" question — the exam likes symptoms that seem to point one direction but resolve to something mundane.

---

## 🚨 Common Mistakes

- Replacing the motherboard or CPU before testing RAM — RAM failures are far more common and cheaper to rule out first
- Treating "no power" as automatically a dead PSU without checking the outlet, power cable, and power switch first (question the obvious)
- Ignoring a burning smell or capacitor swelling as a "minor" symptom — these are safety-critical and mean stop using the system
- Assuming inaccurate date/time is a software/OS problem rather than a CMOS battery nearing end of life

---

## 💡 Wisdom from Mímir

Power issues cascade upward: outlet → cable → PSU → motherboard → component. Test in that order, cheapest and simplest first, before assuming the expensive part failed.

---

## 🔗 Related Notes

- [[Hardware and Network Troubleshooting]] — methodology and overview
- [[Selecting the Right Power Supply for a Build]] — drill note
- [[A+ Core 1 Codex]]
