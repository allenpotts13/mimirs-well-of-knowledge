---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA A+
difficulty: Intermediate
tags:
  - a-plus-1202
  - security
  - mobile-devices
aliases:
  - Mobile Device Security Choice
publish: true
permalink: comptia-a-core-2-220-1202/securing-mobile-devices
---

# <span class="rune">ᚲ</span> Securing Mobile Devices

> *A lost phone and a stolen phone need the same three responses, in order: locate, wipe, restore.*

---

## 🎯 Problem

A scenario describes a mobile device security requirement, loss, or theft. Identify the correct security method.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "device is lost and needs to be found" | Locator application |
| "device is lost/stolen and data must not be accessed" | Remote wipe |
| "wiped device's data needs to be restored to a new phone" | Remote backup application (used beforehand) |
| "unlock method should use a face or fingerprint" | Biometric screen lock |
| "corporate policy needs to push settings automatically" | MDM configuration profile |
| "personal phone gets limited corporate access" | BYOD policy under MDM |
| "repeated failed unlock attempts should trigger a response" | Failed login attempt restrictions |
| "device software needs to stay current against vulnerabilities" | Patch management (OS + app updates) |
| "malicious apps need to be blocked before installation" | Endpoint security software (antivirus/content filtering) |

---

## 🧠 Why This Pattern Works

Mobile security layers stack in a predictable order: **prevent** (encryption, screen lock, patch management, endpoint security) → **detect/respond to loss** (locator apps, failed-login restrictions) → **contain the damage** (remote wipe) → **recover** (remote backup restored to a new device). A scenario describing an already-lost device points to the detect/contain/recover layer; a scenario describing ongoing device management points to the prevent layer.

---

## 🚨 Common Mistakes

- Reaching for remote wipe before confirming the device truly can't be recovered — locator apps come first if there's any chance of recovery
- Assuming BYOD devices are unmanaged — MDM still applies policy, just scoped to protect corporate data specifically
- Forgetting remote backup only helps if it was configured *before* the loss — it's a preventive measure, not a response

---

## 💡 Wisdom from Mímir

Order matters: locate, then decide whether to wipe, then restore from a backup that should have already existed. A scenario testing this sequence is testing whether you'll wipe first and ask questions later.

---

## 🔗 Related Notes

- [[Workstation Hardening, Mobile Security, and Data Destruction]] — full concept writeup
- [[A+ Core 2 Codex]]
