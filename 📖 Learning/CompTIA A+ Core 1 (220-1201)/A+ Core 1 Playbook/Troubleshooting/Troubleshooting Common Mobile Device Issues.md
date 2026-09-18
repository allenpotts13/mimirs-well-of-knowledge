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
  - mobile-devices
aliases:
  - Troubleshooting Mobile Devices
publish: true
permalink: comptia-a-core-1-220-1201/troubleshooting-common-mobile-device-issues
---

# <span class="rune">ᚲ</span> Troubleshooting Common Mobile Device Issues

> *A swollen battery is a safety issue first and a repair ticket second.*

---

## 🎯 Problem

A scenario describes a malfunctioning mobile device. Diagnose the likely hardware or software cause.

---

## 🤔 Mental Model

| Symptom | Likely cause |
|---|---|
| Poor battery health, improper charging | Aging battery or wrong/damaged charger |
| Swollen battery | Safety hazard — stop charging/using immediately, replace |
| Broken screen, physically damaged ports | Physical damage |
| Liquid damage | Corrosion risk — power off immediately, do not charge |
| Overheating | Background processes, failing battery, or blocked ventilation |
| Poor/no connectivity | Radio hardware, carrier/Wi-Fi settings, or SIM issue |
| Digitizer issues, cursor drift/touch calibration | Digitizer hardware fault or needs recalibration |
| Unable to install new applications | Insufficient storage, OS version incompatibility, or MDM restriction |
| Stylus does not work | Stylus/digitizer pairing or battery (active styluses) |
| Degraded performance | Storage nearly full, background apps, malware, or aging battery throttling |
| Malware | Unexpected behavior, pop-ups, battery drain, data usage spikes |

---

## 🧠 Why This Pattern Works

Mobile symptoms split into **safety-critical hardware** (swollen battery, liquid damage — act first, diagnose later), **physical damage** (screen, ports, digitizer), and **software/performance** issues (storage, malware, MDM policy) that look like hardware failure but aren't. The exam rewards recognizing which bucket a symptom belongs to before proposing a fix.

---

## 🚨 Common Mistakes

- Continuing to charge or use a device with a swollen battery — this is a fire/chemical hazard, not a routine repair
- Attempting to power on and charge a liquid-damaged device immediately — this risks corrosion damage and short circuits
- Assuming degraded performance is always a hardware/battery issue — malware and a nearly-full storage drive are just as common
- Treating "can't install new apps" as a connectivity problem when it's usually storage space or an MDM policy restriction

---

## 💡 Wisdom from Mímir

Two mobile symptoms override the normal troubleshooting order entirely: swollen battery and liquid damage. Handle the safety response before you do anything resembling normal diagnosis.

---

## 🔗 Related Notes

- [[Hardware and Network Troubleshooting]] — methodology and overview
- [[Mobile Devices]] — full concept writeup
- [[A+ Core 1 Codex]]
