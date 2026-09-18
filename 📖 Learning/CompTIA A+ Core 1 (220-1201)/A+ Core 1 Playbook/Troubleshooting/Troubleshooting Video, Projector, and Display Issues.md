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
  - Troubleshooting Display Issues
publish: true
permalink: comptia-a-core-1-220-1201/troubleshooting-video-projector-and-display-issues
---

# <span class="rune">ᚲ</span> Troubleshooting Video, Projector, and Display Issues

> *Most "broken screen" scenarios are a cable, a source setting, or a bulb — not the panel itself.*

---

## 🎯 Problem

A scenario describes a display or projector showing a visual defect or failing to show anything. Diagnose the likely cause.

---

## 🤔 Mental Model

| Symptom | Likely cause |
|---|---|
| No image, screen is on | Incorrect input source selected |
| No image at all, various cabling symptoms | Physical cabling issue — reseat/replace the cable first |
| Fuzzy or distorted image | Wrong native resolution set, or a failing cable/connector |
| Dim image | Failing backlight/inverter (LCD), or a dying projector bulb |
| Flashing screen, intermittent shutdown (projector) | Overheating or a failing bulb/ballast |
| Dead pixels | Physical panel defect — not fixable by settings |
| Display burn-in | Static image displayed too long — panel-level, not fixable |
| Incorrect color display | Cable issue, wrong color profile, or panel failure |
| Sizing/distortion issues | Wrong resolution or aspect ratio setting |
| Audio issues (on an AV cable) | Check whether the cable/port actually carries audio (e.g., some DVI doesn't) |

---

## 🧠 Why This Pattern Works

Display troubleshooting follows a strict cheapest-first order: **input source → cable → settings (resolution/refresh) → hardware (panel/bulb/backlight)**. Most reported "broken screen" symptoms resolve at the first two steps. Symptoms like dead pixels and burn-in are the exception — they're physical panel defects with no software fix, and the exam expects you to recognize when to stop troubleshooting and replace/repair hardware instead.

---

## 🚨 Common Mistakes

- Replacing a display for "no image" without first checking the input source selection and cable seating
- Treating dead pixels or burn-in as something a driver update or setting can fix — they're physical defects
- Assuming all video cables carry audio — some (older DVI, VGA) don't, which explains "video works, no sound" scenarios
- Overlooking a failing projector bulb as the cause of dimness or intermittent shutdown from overheating

---

## 💡 Wisdom from Mímir

Before touching hardware, cycle the input source and reseat the cable — those two steps resolve more "broken display" tickets than any component replacement.

---

## 🔗 Related Notes

- [[Hardware and Network Troubleshooting]] — methodology and overview
- [[A+ Core 1 Codex]]
