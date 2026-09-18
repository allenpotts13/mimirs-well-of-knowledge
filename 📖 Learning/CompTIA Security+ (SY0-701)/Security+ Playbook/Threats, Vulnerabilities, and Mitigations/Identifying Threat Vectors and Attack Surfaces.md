---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Security+
difficulty: Beginner
tags:
  - security-plus
  - threats-vulnerabilities
aliases:
  - Threat Vector Identification
publish: true
permalink: comptia-security-sy0-701/identifying-threat-vectors-and-attack-surfaces
---

# <span class="rune">ᚲ</span> Identifying Threat Vectors and Attack Surfaces

> *The vector is the delivery truck. The payload is what's inside it. Name the truck first.*

---

## 🎯 Problem

A scenario describes how an attack was delivered or an exposure that made it possible. Identify the threat vector or attack surface.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "malicious link sent in an email" | Message-based (email) vector |
| "malicious link sent via text message" | Message-based (SMS) vector |
| "malicious code embedded in an image file" | Image-based vector |
| "infected USB drive left in a parking lot" | Removable device vector |
| "attacker calls pretending to be IT support" | Voice call vector (vishing) |
| "unpatched software still running past its support date" | Unsupported systems/applications vector |
| "device found with a factory-default admin password" | Default credentials vector |
| "compromise via a trusted vendor's software update" | Supply chain vector |
| "open, unused network port discovered during a scan" | Open service ports vector |

---

## 🧠 Why This Pattern Works

Threat vectors describe the *path* an attack takes in, independent of what the payload does once delivered. Grouping by delivery channel (message, image, file, voice, removable media) versus by exposure (unsupported systems, default credentials, open ports, unsecure networks, supply chain) covers the full list — a scenario naming *how something arrived* points to the first group, while a scenario naming *what was left exposed* points to the second.

---

## 🚨 Common Mistakes

- Naming the malware type instead of the vector when asked how an attack was delivered
- Treating supply chain as a rare/exotic vector rather than a common, high-impact real-world category
- Assuming default credentials only apply to consumer devices — enterprise network gear is frequently deployed with defaults still active
- Confusing an attack surface (everything exposed) with a single vector (one specific path in) — an attack surface is the sum of all vectors

---

## 💡 Wisdom from Mímir

Ask "how did it get in the door?" not "what did it do once inside?" That question keeps vector-identification questions separate from attack-type questions.

---

## 🔗 Related Notes

- [[Threat Actors and Attack Vectors]] — full concept writeup
- [[Security+ Codex]]
