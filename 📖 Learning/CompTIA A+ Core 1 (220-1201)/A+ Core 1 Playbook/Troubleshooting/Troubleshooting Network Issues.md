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
  - networking
aliases:
  - Troubleshooting Connectivity Issues
publish: true
permalink: comptia-a-core-1-220-1201/troubleshooting-network-issues
---

# <span class="rune">ᚲ</span> Troubleshooting Network Issues

> *"Slow" and "intermittent" are two completely different problems that get confused constantly.*

---

## 🎯 Problem

A scenario describes degraded or failed network connectivity. Diagnose the likely cause.

---

## 🤔 Mental Model

| Symptom | Likely cause |
|---|---|
| Intermittent wireless connectivity | Interference, weak signal, or AP overload |
| Slow network speeds | Bandwidth saturation, outdated hardware, or wrong band (2.4 vs 5GHz) |
| Limited connectivity | Device got an address but can't fully reach the network (often APIPA or wrong subnet) |
| Jitter, poor VoIP quality | Inconsistent latency — congestion or QoS misconfiguration |
| Port flapping | Physical layer issue (bad cable/port) or a duplex mismatch |
| High latency | Distance, congestion, or routing inefficiency |
| External interference | Other wireless devices/equipment on the same frequency |
| Authentication failures | Wrong credentials, expired certificate, or misconfigured security protocol |
| Intermittent internet connectivity | ISP-side issue, modem/router instability, or overheating equipment |

---

## 🧠 Why This Pattern Works

Separate *connectivity* symptoms (can't connect at all, or connection drops) from *quality* symptoms (slow, jittery, laggy but connected). Connectivity symptoms point toward authentication, addressing, or physical layer causes; quality symptoms point toward congestion, interference, or capacity limits. "Limited connectivity" specifically (Windows' own phrasing) usually means the device got *some* network response (like an APIPA address) but not a full, working configuration — tying directly back to [[Configuring a Basic SOHO Network]].

---

## 🚨 Common Mistakes

- Treating "slow" and "intermittent" as the same troubleshooting path — slow points to capacity/interference, intermittent points to a flapping physical or authentication issue
- Assuming jitter and high latency are the same measurement — latency is delay, jitter is the *variation* in delay, and VoIP specifically suffers from jitter even when average latency looks fine
- Overlooking port flapping as a *physical* symptom (bad cable, bad port, duplex mismatch) rather than jumping to software/configuration causes
- Forgetting authentication failures can stem from an expired certificate, not just a wrong password

---

## 💡 Wisdom from Mímir

Ask "does it connect at all?" before asking "how well does it perform?" — those are two different troubleshooting trees, and mixing them wastes time chasing the wrong layer.

---

## 🔗 Related Notes

- [[Hardware and Network Troubleshooting]] — methodology and overview
- [[Configuring a Basic SOHO Network]] — drill note
- [[A+ Core 1 Codex]]
