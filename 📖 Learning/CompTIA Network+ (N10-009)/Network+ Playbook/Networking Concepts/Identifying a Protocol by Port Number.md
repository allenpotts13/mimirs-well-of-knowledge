---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Network+
difficulty: Beginner
tags:
  - network-plus
  - networking-concepts
aliases:
  - Port Number Identification
publish: true
permalink: comptia-network-n10-009/identifying-a-protocol-by-port-number
---

# <span class="rune">ᚲ</span> Identifying a Protocol by Port Number

> *This is flashcard territory — 20 ports, no shortcuts, memorize them cold.*

---

## 🎯 Problem

A scenario names a port number or a symptom involving a specific protocol. Identify the protocol, or vice versa.

---

## 🤔 Mental Model

| Port | Protocol | Port | Protocol |
|---|---|---|---|
| 20/21 | FTP | 161/162 | SNMP |
| 22 | SFTP/SSH | 389 | LDAP |
| 23 | Telnet | 443 | HTTPS |
| 25 | SMTP | 445 | SMB |
| 53 | DNS | 514 | Syslog |
| 67/68 | DHCP | 587 | SMTPS |
| 69 | TFTP | 636 | LDAPS |
| 80 | HTTP | 1433 | SQL Server |
| 123 | NTP | 3389 | RDP |
| | | 5060/5061 | SIP |

---

## 🧠 Why This Pattern Works

Group ports by function to make memorization stick: **file transfer** (20/21 FTP, 22 SFTP, 69 TFTP), **remote access** (22 SSH, 23 Telnet, 3389 RDP), **mail** (25 SMTP, 587 SMTPS), **directory/security** (389 LDAP, 636 LDAPS), **name/time/management** (53 DNS, 67/68 DHCP, 123 NTP, 161/162 SNMP), and **web/voice/data** (80 HTTP, 443 HTTPS, 445 SMB, 1433 SQL, 5060/5061 SIP). The "secure" variant of a protocol is almost always a different, higher port than its plaintext counterpart (21→22, 80→443, 389→636, 25→587).

---

## 🚨 Common Mistakes

- Confusing port 22 (SSH/SFTP) with port 21 (FTP control channel) — SFTP rides over SSH entirely, it's not FTP-plus-encryption
- Mixing up 161 (SNMP agent queries) with 162 (SNMP trap notifications)
- Forgetting SIP uses two ports (5060 unencrypted, 5061 encrypted/TLS) for call signaling

---

## 💡 Wisdom from Mímir

When two ports for the same protocol family appear, the higher number is almost always the encrypted/secure variant — 587 vs. 25, 636 vs. 389, 443 vs. 80. That pattern alone gets you most of the "secure version" questions right.

---

## 🔗 Related Notes

- [[Ports, Protocols, Media, and Topologies]] — full concept writeup
- [[Network+ Codex]]
