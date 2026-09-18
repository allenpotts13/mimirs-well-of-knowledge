---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Network+
difficulty: Intermediate
tags:
  - network-plus
  - network-troubleshooting
aliases:
  - Troubleshooting Latency and Jitter
publish: true
permalink: comptia-network-n10-009/troubleshooting-network-performance-issues
---

# <span class="rune">ᚲ</span> Troubleshooting Network Performance Issues

> *Latency, jitter, and packet loss are three different measurements — a fix for one won't necessarily touch the others.*

---

## 🎯 Problem

A scenario describes degraded network performance, wired or wireless. Diagnose the likely cause.

---

## 🤔 Mental Model

| Symptom | Likely cause |
|---|---|
| Congestion/contention | Too many devices competing for shared bandwidth |
| Bottlenecking | One undersized link/device constraining overall throughput |
| Low throughput vs. advertised bandwidth | Capacity mismatch, duplex issue, or bottleneck upstream |
| High latency | Distance, congestion, or inefficient routing path |
| Packet loss | Congestion, faulty hardware, or wireless interference |
| Jitter | Inconsistent latency — problematic specifically for VoIP/video |
| Wireless interference / channel overlap | Overlapping APs or non-Wi-Fi devices on the same frequency |
| Insufficient wireless coverage | AP placement or power gaps |
| Client disassociation | Weak signal, interference, or roaming misconfiguration |
| Roaming misconfiguration | Client doesn't hand off cleanly between APs |

---

## 🧠 Why This Pattern Works

Performance troubleshooting separates into **capacity problems** (congestion, contention, bottlenecking — too much demand or too little supply) and **quality problems** (latency, jitter, packet loss — the connection works but poorly) with a **wireless-specific** subset (interference, coverage, roaming) layered on top for Wi-Fi scenarios. VoIP and video calls are especially sensitive to jitter even when average latency looks acceptable, which is why jitter gets called out as its own measurement rather than folded into latency.

---

## 🚨 Common Mistakes

- Treating latency and jitter as the same measurement — latency is the delay itself, jitter is the *variation* in that delay over time
- Assuming more bandwidth fixes latency — bandwidth affects throughput capacity, not the delay per packet
- Diagnosing wireless disassociation as a wired network problem — check signal strength/interference and roaming behavior first
- Overlooking channel overlap as a cause of poor wireless performance in dense environments (apartments, offices with many APs)

---

## 💡 Wisdom from Mímir

Sort the symptom into "not enough capacity" or "inconsistent quality" first — that single sort points you toward bandwidth/congestion fixes or toward latency/jitter/interference fixes respectively.

---

## 🔗 Related Notes

- [[Network Troubleshooting Methodology and Overview]] — methodology and overview
- [[Network+ Codex]]
