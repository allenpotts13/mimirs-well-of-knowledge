---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA A+
difficulty: Beginner
tags:
  - a-plus-1201
  - networking
aliases:
  - Wireless Standards Choice
publish: true
permalink: comptia-a-core-1-220-1201/identifying-wireless-networking-standards-and-frequencies
---

# <span class="rune">ᚲ</span> Identifying Wireless Networking Standards and Frequencies

> *Range vs. speed vs. interference — every wireless frequency question is really about this trade-off.*

---

## 🎯 Problem

A scenario describes a wireless requirement (range, speed, interference, short-range pairing). Identify the right frequency band or wireless technology.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "needs the longest range, tolerates slower speed, lots of walls" | 2.4GHz |
| "needs the fastest speed, shorter range, less interference" | 5GHz or 6GHz |
| "many overlapping networks nearby causing congestion" | Move to a less crowded band/channel (5GHz/6GHz, or a specific channel) |
| "tap-to-pay or tap-to-pair, inches of range" | NFC |
| "pair a headset or keyboard, several feet of range" | Bluetooth |
| "inventory tag read from a short distance without contact" | RFID |
| "identify which 802.11 generation supports the fastest theoretical speed" | Check the specific 802.11 standard named in the scenario |

---

## 🧠 Why This Pattern Works

Lower frequencies (2.4GHz) travel farther and penetrate obstacles better but carry less data and face more interference (cordless phones, microwaves, other 2.4GHz networks). Higher frequencies (5GHz, 6GHz) trade range for speed and a cleaner spectrum. Short-range technologies (NFC, Bluetooth, RFID) aren't about "frequency" trade-offs at all — they're about proximity and pairing model.

---

## 🚨 Common Mistakes

- Assuming 5GHz always "wins" — it has a shorter effective range and worse wall penetration than 2.4GHz
- Confusing RFID (passive/read-only tag scanning) with NFC (two-way, very short range, used for payments/pairing)
- Treating every "802.11" mention as identical — different standards support different max speeds and bands

---

## 💡 Wisdom from Mímir

When a wireless scenario is really about interference or dead zones, the fix is almost never "buy a faster router" — it's channel selection, band choice, or access point placement.

---

## 🔗 Related Notes

- [[Networking Fundamentals]] — full concept writeup
- [[A+ Core 1 Codex]]
