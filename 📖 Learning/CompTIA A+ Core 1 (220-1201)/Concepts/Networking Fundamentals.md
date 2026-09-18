---
type: concept
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA A+
difficulty: Beginner
tags:
  - a-plus-1201
  - networking
aliases:
  - A+ Networking Fundamentals
  - TCP UDP Ports and Protocols
publish: true
permalink: comptia-a-core-1-220-1201/networking-fundamentals
---

# <span class="rune">ᛟ</span> Networking Fundamentals

> *Every port number on this list is a fact the exam expects memorized, not reasoned out.*

---

## 🎯 Purpose

Covers roughly half of the 23%-weighted Networking domain (2.1-2.4): TCP vs. UDP ports and protocols, wireless technologies, services provided by networked hosts, and network configuration concepts. This is the vocabulary layer — [[Networking Hardware and SOHO Setup]] covers the physical/configuration layer built on top of it.

---

## 🧠 Key Ideas

- **TCP** is connection-oriented and guarantees delivery (handshake, acknowledgment, retransmission); **UDP** is connectionless and fast, with no delivery guarantee — the trade-off is reliability vs. speed
- A specific set of port numbers is exam-testable by heart, not looked up
- Wireless technologies split into **frequencies** (2.4/5/6GHz), **standards** (802.11 family), and **short-range** tech (Bluetooth, NFC, RFID)
- "Services provided by networked hosts" is really just: know what a DNS/DHCP/mail/print/file/database/syslog/NTP server *does*
- Network configuration concepts (DNS record types, DHCP lease lifecycle, VLAN/VPN) are the "how it's set up" layer above the "what it is" layer

---

## ⚙️ How It Works

### Common ports and protocols (memorize these)

| Port | Protocol |
|---|---|
| 20-21 | FTP |
| 22 | SSH |
| 23 | Telnet |
| 25 | SMTP |
| 53 | DNS |
| 67/68 | DHCP |
| 80 | HTTP |
| 110 | POP3 |
| 137-139 | NetBIOS/NetBT |
| 143 | IMAP |
| 389 | LDAP |
| 443 | HTTPS |
| 445 | SMB/CIFS |
| 3389 | RDP |

### TCP vs. UDP

| | TCP | UDP |
|---|---|---|
| Connection | Connection-oriented (handshake) | Connectionless |
| Reliability | Guaranteed delivery, ordered | Best-effort, no guarantee |
| Speed | Slower (overhead) | Faster (no overhead) |
| Example use | HTTP, FTP, SSH, email | DNS lookups, streaming, VoIP |

### Wireless networking technologies

| Layer | Options |
|---|---|
| Frequencies | 2.4GHz, 5GHz, 6GHz |
| Channels | Regulations, selection, widths, bands |
| Standards | 802.11 family |
| Short-range | Bluetooth, NFC, RFID |

### Server roles (services provided by networked hosts)

| Role | Purpose |
|---|---|
| DNS | Name resolution |
| DHCP | Automatic IP assignment |
| Fileshare / Print server | Shared storage / shared printing |
| Mail server | Send/receive email |
| Web server | Serves HTTP(S) content |
| AAA | Authentication, Authorization, Accounting |
| Database server | Structured data storage |
| Syslog | Centralized logging |
| NTP | Time synchronization |

Also know: internet appliances (spam gateways, UTM, load balancers, proxy servers), legacy/embedded systems (SCADA), and IoT devices as host categories.

### Network configuration concepts

**DNS record types:**

| Record | Purpose |
|---|---|
| A | Hostname → IPv4 |
| AAAA | Hostname → IPv6 |
| CNAME | Alias to another hostname |
| MX | Mail exchange server |
| TXT | Arbitrary text — SPF/DKIM/DMARC spam management live here |

**DHCP concepts:** Lease → Reservation → Scope → Exclusion. A **lease** is a temporary IP assignment; a **reservation** ties a specific MAC address to a specific IP within the scope; **exclusions** carve out addresses DHCP should never hand out (usually reserved for static devices).

**VLAN vs. VPN:** a VLAN segments a *local* network logically without new cabling; a VPN extends a *private* network securely across a public one (the internet).

---

## 💻 Examples

```text
"A file transfer needs to guarantee every byte arrives and in order."
→ TCP

"An application streams live video and can tolerate a dropped frame but
not lag."
→ UDP

"A technician needs to remotely administer a Linux server securely."
→ SSH, port 22

"A domain's email keeps landing in spam and needs an authentication record
published."
→ TXT record (SPF/DKIM/DMARC)

"Two departments share a switch but should not see each other's broadcast
traffic without adding new physical cabling."
→ VLAN
```

---

## 🚨 Common Mistakes

- Mixing up port 143 (IMAP) and port 110 (POP3) — IMAP keeps mail on the server and syncs across devices, POP3 downloads and often removes it
- Assuming UDP is "worse" than TCP — it's a deliberate trade-off for speed-sensitive traffic like VoIP and DNS
- Forgetting DHCP reservations still come from within the defined scope — they're not a separate address pool
- Treating a VPN and a VLAN as solving the same problem — one is about segmentation, the other about secure remote access

---

## 📖 Further Reading

- [CompTIA A+ Core 1 (220-1201) official exam objectives](https://www.comptia.org/certifications/a)

---

## 🔗 Related Notes

- [[Networking Hardware and SOHO Setup]]
- [[TCP vs UDP - Choosing the Right Transport Protocol]] — drill note
- [[Identifying Wireless Networking Standards and Frequencies]] — drill note
- [[A+ Core 1 Codex]]
