---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA A+
difficulty: Beginner
tags:
  - a-plus-1201
  - hardware
aliases:
  - RAM Selection
publish: true
permalink: comptia-a-core-1-220-1201/choosing-the-right-ram-for-a-build
---

# <span class="rune">ᚲ</span> Choosing the Right RAM for a Build

> *Form factor gets it in the slot. Generation gets it to actually work.*

---

## 🎯 Problem

A scenario describes a system (desktop, laptop, server) that needs RAM installed or upgraded. Identify the correct form factor, generation, or type.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "laptop" | SODIMM |
| "desktop or server" | DIMM |
| "server that must detect/correct memory errors" | ECC RAM |
| "consumer desktop, cost-sensitive" | Non-ECC RAM |
| "maximize memory bandwidth using multiple identical sticks" | Matched RAM in a channel configuration |
| "new RAM won't POST despite fitting the slot" | Wrong DDR generation — check compatibility, not just physical fit |

---

## 🧠 Why This Pattern Works

RAM compatibility is layered: form factor gets the stick physically into the socket, DDR generation determines electrical/signaling compatibility (and generations are not interchangeable despite sometimes looking similar), and ECC vs. non-ECC is a support decision the motherboard/CPU must explicitly allow. A stick that "fits" can still fail to POST if any of these layers mismatch.

---

## 🚨 Common Mistakes

- Assuming a RAM stick that physically fits will work — DDR generations use different notch placement specifically to prevent this, but exam scenarios test the underlying concept
- Installing ECC RAM in a board that doesn't support it (or vice versa) expecting it to "just work" in a degraded mode
- Installing mismatched stick sizes/speeds and expecting full dual/quad-channel performance — channel configurations want matched RAM

---

## 💡 Wisdom from Mímir

When a build scenario asks "why won't this POST," check RAM generation and channel-matching before assuming a defective stick — mismatches look identical to failures from the outside.

---

## 🔗 Related Notes

- [[Core Hardware Components]] — full concept writeup
- [[A+ Core 1 Codex]]
