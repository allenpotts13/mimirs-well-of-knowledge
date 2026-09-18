---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Network+
difficulty: Intermediate
tags:
  - network-plus
  - network-operations
  - dns
aliases:
  - DNS Record Type Choice
publish: true
permalink: comptia-network-n10-009/identifying-dns-record-types-and-implementing-ip-services
---

# <span class="rune">ᚲ</span> Identifying DNS Record Types and Implementing IP Services

> *Every DNS record type answers exactly one question — name it and the record type follows.*

---

## 🎯 Problem

A scenario describes a name resolution or IP service requirement. Identify the correct DNS record type or addressing service.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "resolve a hostname to an IPv4 address" | A record |
| "resolve a hostname to an IPv6 address" | AAAA record |
| "alias one hostname to another" | CNAME record |
| "identify where a domain's mail should be delivered" | MX record |
| "publish arbitrary text for SPF/DKIM/DMARC" | TXT record |
| "resolve an IP address back to a hostname" | PTR record (reverse zone) |
| "identify the authoritative nameservers for a domain" | NS record |
| "device needs a specific IP every time via DHCP" | DHCP reservation |
| "IPv6 host self-configures with no DHCP server" | SLAAC |
| "DNS responses need cryptographic authenticity" | DNSSEC |
| "DNS queries need to be encrypted in transit" | DoH or DoT |

---

## 🧠 Why This Pattern Works

Each DNS record type is purpose-built: A/AAAA map names to addresses, CNAME creates aliases, MX routes mail specifically, TXT carries free-form text (used heavily for anti-spoofing records), NS declares authority, and PTR uniquely requires a *reverse* zone since it goes the opposite direction (IP→name) from every other record type listed. DNSSEC and DoH/DoT solve different trust problems — one authenticates the answer, the other encrypts the question.

---

## 🚨 Common Mistakes

- Trying to look up a PTR record in a forward zone — reverse lookups require a separate reverse zone entirely
- Using a CNAME where an A record is required (e.g., at a domain's root/apex, where CNAMEs are often disallowed)
- Confusing DNSSEC (authenticates responses) with DoH/DoT (encrypts the query channel) as solving the same problem
- Assuming SLAAC and DHCPv6 are mutually exclusive — a network can use either or both together

---

## 💡 Wisdom from Mímir

Ask "what does this record need to translate into what?" — name→IPv4 (A), name→IPv6 (AAAA), name→name (CNAME), domain→mail server (MX), IP→name (PTR). The direction and data type of the translation names the record.

---

## 🔗 Related Notes

- [[DNS, DHCP, and IP Network Services]] — full concept writeup
- [[Network+ Codex]]
