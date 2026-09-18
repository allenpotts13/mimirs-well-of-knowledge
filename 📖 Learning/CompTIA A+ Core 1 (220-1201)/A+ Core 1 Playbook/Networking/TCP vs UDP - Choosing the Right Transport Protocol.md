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
  - TCP or UDP
publish: true
permalink: comptia-a-core-1-220-1201/tcp-vs-udp-choosing-the-right-transport-protocol
---

# <span class="rune">ᚲ</span> TCP vs UDP - Choosing the Right Transport Protocol

> *Guaranteed and ordered, or fast and disposable — pick one.*

---

## 🎯 Problem

A scenario describes an application's traffic. Decide whether it rides on TCP or UDP, and often, which port it uses.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "every byte must arrive, in order, or the file is corrupt" | TCP |
| "a dropped packet is acceptable, but delay is not" | UDP |
| "web browsing, email, remote file transfer" | TCP (HTTP/HTTPS, SMTP/IMAP/POP3, FTP) |
| "live video call, DNS lookup, streaming" | UDP |
| "remote command-line session that must not lose keystrokes" | TCP (SSH, port 22) |
| "one-time broadcast where speed matters more than reliability" | UDP |

---

## 🧠 Why This Pattern Works

TCP spends overhead (handshake, acknowledgment, retransmission) to guarantee delivery — worth it when correctness matters more than speed. UDP skips all of that overhead — worth it when the application can tolerate loss but not latency, or handles reliability itself at a higher layer (e.g., video codecs conceal dropped frames). The exam's scenario almost always signals which trade-off matters more.

---

## 🚨 Common Mistakes

- Assuming DNS is always TCP because it's "important" — standard DNS lookups use UDP (port 53); DNS falls back to TCP only for large responses/zone transfers
- Treating UDP as inherently unreliable/bad — it's a deliberate design choice for latency-sensitive traffic, not a lesser protocol
- Forgetting VoIP and streaming use UDP specifically because retransmitting a late packet is worse than just dropping it

---

## 💡 Wisdom from Mímir

Ask "would a resend fix this, or would a resend already be too late?" A resend fixing it means TCP. A resend being too late (a live call, a live stream) means UDP.

---

## 🔗 Related Notes

- [[Networking Fundamentals]] — full concept writeup
- [[A+ Core 1 Codex]]
