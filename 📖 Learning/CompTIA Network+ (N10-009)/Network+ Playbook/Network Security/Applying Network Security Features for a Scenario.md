---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Network+
difficulty: Intermediate
tags:
  - network-plus
  - network-security
aliases:
  - Network Defense Feature Choice
publish: true
permalink: comptia-network-n10-009/applying-network-security-features-for-a-scenario
---

# <span class="rune">ᚲ</span> Applying Network Security Features for a Scenario

> *NAC decides who gets on the network. ACLs decide what they can do once they're there. Different gates, different guards.*

---

## 🎯 Problem

A scenario describes a network security requirement. Choose the correct defense feature or technique.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "only pre-approved MAC addresses may connect to this switch port" | Port security / MAC filtering |
| "devices must authenticate before joining the network at all" | 802.1X (NAC) |
| "control what traffic is allowed between subnets" | ACL |
| "block access to specific categories of websites" | Content filtering |
| "block access to specific URLs" | URL filtering |
| "unused switch ports and services should be disabled by default" | Device hardening |
| "default credentials were never changed on network gear" | Change default passwords |
| "a public-facing service must be reachable without exposing the internal network" | Screened subnet |
| "internal network traffic should never mix with a guest/IoT segment" | Zone separation (trusted vs. untrusted) |

---

## 🧠 Why This Pattern Works

Defense techniques split into **access control** (NAC/802.1X/port security/MAC filtering — who gets on the network), **traffic control** (ACLs/URL/content filtering — what's allowed once connected), and **baseline hardening** (disabling unused ports/services, changing defaults — reducing what's exposed in the first place). Zones and screened subnets are architectural: they physically or logically separate traffic by trust level so a compromise in one zone doesn't automatically reach another.

---

## 🚨 Common Mistakes

- Reaching for an ACL when the real requirement is about *who* connects, not what traffic passes — that's NAC/802.1X territory
- Assuming device hardening is a one-time setup rather than an ongoing practice (new unused services appear as systems change)
- Treating a screened subnet as unnecessary if a firewall exists — the screened subnet is specifically about isolating what's public-facing from what's internal, a firewall alone doesn't guarantee that separation
- Confusing content filtering (category-based) with URL filtering (specific address-based) as identical

---

## 💡 Wisdom from Mímir

Ask "is this about letting someone onto the network, or controlling what they can do once they're on it?" That question alone sorts NAC-family answers from ACL-family answers every time.

---

## 🔗 Related Notes

- [[Network Attacks and Defense Techniques]] — full concept writeup
- [[Network+ Codex]]
