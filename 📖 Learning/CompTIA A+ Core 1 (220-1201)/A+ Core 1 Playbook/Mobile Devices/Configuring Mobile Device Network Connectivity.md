---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA A+
difficulty: Beginner
tags:
  - a-plus-1201
  - mobile-devices
aliases:
  - Mobile Device Network Setup
publish: true
permalink: comptia-a-core-1-220-1201/configuring-mobile-device-network-connectivity
---

# <span class="rune">ᚲ</span> Configuring Mobile Device Network Connectivity

> *Cellular, Wi-Fi, and Bluetooth each have their own setup checklist — the exam expects you to know which step belongs to which.*

---

## 🎯 Problem

A scenario describes a mobile device that needs cellular, Wi-Fi, or Bluetooth connectivity configured, or corporate policy applied via MDM. Identify the correct setup step or classification.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "phone has no signal and uses a removable chip for carrier identity" | SIM (or eSIM if built-in/no physical card) |
| "share a phone's cellular connection with a laptop" | Hotspot |
| "connect a headset for the first time" | Bluetooth pairing — enable, make discoverable, PIN, test |
| "track a lost device's physical location" | GPS / cellular location services |
| "employee's personal phone gets limited corporate email access" | BYOD under MDM |
| "company-owned phone is locked to only approved apps" | Corporate device configuration under MDM |
| "contacts and calendar are out of sync with the desktop" | Mobile device synchronization |
| "sync is using far more cellular data than expected" | Data cap not accounted for in sync settings |

---

## 🧠 Why This Pattern Works

Mobile network scenarios almost always name the *transport* (cellular, Wi-Fi, Bluetooth) and the *task* (pair, enable, authenticate) separately. Bluetooth pairing specifically follows a fixed sequence — enable Bluetooth, enable pairing/discoverability, find the device, enter a PIN, test connectivity — and exam questions often ask you to identify which step in that sequence is missing.

---

## 🚨 Common Mistakes

- Treating eSIM and SIM as functionally different from the carrier's perspective — the difference is physical (removable chip) vs. embedded, not what they do
- Assuming BYOD means no MDM policy applies — enrollment still allows policy enforcement, just scoped differently than a corporate-owned device
- Forgetting a hotspot's data usage counts against the *phone's* cellular data plan, not the connected device's

---

## 💡 Wisdom from Mímir

If a mobile networking question feels ambiguous, ask "whose data plan is this?" — it resolves hotspot vs. Wi-Fi confusion immediately, and it's usually the crux of the scenario.

---

## 🔗 Related Notes

- [[Mobile Devices]] — full concept writeup
- [[A+ Core 1 Codex]]
