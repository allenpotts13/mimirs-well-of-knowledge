---
type: concept
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Network+
difficulty: Intermediate
tags:
  - network-plus
  - network-operations
  - dns
  - dhcp
aliases:
  - Network+ DNS
  - Network+ DHCP
publish: true
permalink: comptia-network-n10-009/dns-dhcp-and-ip-network-services
---

# <span class="rune">ᛟ</span> DNS, DHCP, and IP Network Services

> *DNS and DHCP are the two services that make IP networking usable by humans — and the two most commonly misconfigured.*

---

## 🎯 Purpose

Covers 3.4 of the Network Operations domain: implementing IPv4 and IPv6 network services — dynamic addressing (DHCP/SLAAC), name resolution (DNS and its record types), and time protocols. A "given a scenario" objective.

---

## 🧠 Key Ideas

- **DHCP** hands out addresses dynamically via lease → scope → reservation → exclusion; **SLAAC** is IPv6's equivalent self-configuration mechanism, without requiring a DHCP server
- DNS record types each answer a different question — A/AAAA resolve names to addresses, MX routes mail, CNAME aliases, TXT carries arbitrary text (SPF/DKIM records live here), PTR does *reverse* lookup (IP → name)
- **Forward zones** resolve name→IP; **reverse zones** resolve IP→name — they are separate zone files even for the same domain
- **DNSSEC** adds cryptographic authentication to DNS responses to prevent spoofing; **DoH/DoT** encrypt the DNS *query itself* in transit — different problems, both about DNS trust
- Time protocols (NTP, PTP, NTS) exist because accurate, synchronized time underlies logging, certificates, and authentication — PTP adds sub-microsecond precision NTP can't reach, NTS adds authentication NTP traditionally lacks

---

## ⚙️ How It Works

### DHCP concepts

| Term | Meaning |
|---|---|
| Lease | Temporary IP assignment, expires after lease time |
| Scope | The range of addresses DHCP can assign |
| Reservation | Ties a specific MAC to a specific IP within the scope |
| Exclusion | Addresses in the scope DHCP should never assign |
| Relay/IP helper | Forwards DHCP requests across subnets to a remote DHCP server |
| SLAAC | IPv6 hosts self-configure an address using router advertisements, no DHCP server required |

### DNS record types

| Record | Purpose |
|---|---|
| A | Hostname → IPv4 |
| AAAA | Hostname → IPv6 |
| CNAME | Alias to another hostname |
| MX | Mail exchange server |
| TXT | Arbitrary text — SPF/DKIM/DMARC live here |
| NS | Authoritative nameserver for the zone |
| PTR | IP → hostname (reverse lookup) |

**Zone types:** forward (name→IP) vs. reverse (IP→name); authoritative (the source of truth) vs. non-authoritative (cached copy); primary vs. secondary (replicated); recursive (resolves on behalf of the client, querying other servers as needed).

**DNS security:** DNSSEC (cryptographically signs responses to prevent spoofing/poisoning), DoH/DoT (encrypt the DNS query in transit over HTTPS/TLS respectively).

### Time protocols

| Protocol | Precision | Note |
|---|---|---|
| NTP | Millisecond-level | Standard time sync |
| PTP | Sub-microsecond | Used where NTP isn't precise enough (e.g., financial trading, industrial systems) |
| NTS | NTP + authentication | Prevents time-source spoofing |

---

## 💻 Examples

```text
"A device needs a specific IP address every time it requests one via
DHCP, without manually configuring it statically."
→ DHCP reservation

"An IPv6 host configures its own address using router advertisements,
with no DHCP server present."
→ SLAAC

"A domain's outbound mail server needs to be discoverable by other mail
systems."
→ MX record

"An IP address needs to resolve back to a hostname for a log analysis
tool."
→ PTR record (reverse lookup)

"DNS responses need cryptographic proof they haven't been tampered with
in transit."
→ DNSSEC

"A trading platform needs time synchronization accurate to the
microsecond."
→ PTP
```

---

## 🚨 Common Mistakes

- Confusing DNSSEC (authenticates DNS *responses*) with DoH/DoT (encrypts the DNS *query in transit*) — both improve DNS trust but solve different problems
- Forgetting PTR records require a separate reverse zone, even for a domain that already has forward A records
- Assuming SLAAC and DHCP are interchangeable for IPv6 — SLAAC is self-configuration, DHCPv6 is server-assigned, and networks can use either or both
- Treating NTP as "good enough" for every use case — some systems genuinely need PTP's precision or NTS's authentication

---

## 📖 Further Reading

- [CompTIA Network+ (N10-009) official exam objectives](https://www.comptia.org/certifications/network)

---

## 🔗 Related Notes

- [[Network Monitoring and Access Management]]
- [[Identifying DNS Record Types and Implementing IP Services]] — drill note
- [[Network+ Codex]]
