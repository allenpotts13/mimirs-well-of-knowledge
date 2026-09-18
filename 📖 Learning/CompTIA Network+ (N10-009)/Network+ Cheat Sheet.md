---
type: cheat-sheet
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Network+
tags:
  - network-plus
  - cheat-sheet
aliases:
  - Network+ Quick Reference
publish: true
permalink: comptia-network-n10-009/network-cheat-sheet
---

# <span class="rune">ᛊ</span> Network+ Cheat Sheet

> *Last-mile reference. If you can fill in every row of this from memory, you're ready.*

---

## OSI model

7 Application · 6 Presentation · 5 Session · 4 Transport (ports, TCP/UDP) · 3 Network (IP, routing) · 2 Data Link (MAC, switches) · 1 Physical (cabling)

## Common ports

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

Secure variant = higher port: 21→22(SFTP), 25→587, 80→443, 389→636.

## IPv4 quick reference

Private: 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 · APIPA: 169.254.x.x (DHCP failed) · Loopback: 127.x.x.x · Usable hosts = block size − 2

## Route selection (two separate mechanisms!)

1. **Longest prefix match** (forwarding, always first) — most specific route wins regardless of source
2. **Administrative distance → metric** (table installation) — lowest AD wins between sources for the *same* prefix; metric breaks ties within the same protocol

## Switching

VLAN = logical segmentation (no routing between VLANs without an SVI/router) · 802.1Q = trunk tagging · Native VLAN = untagged on trunk · STP = prevents loops · Link aggregation = combines links for bandwidth

## Wireless

BSSID = one AP's radio · SSID/ESSID = network name (can span APs) · PSK = shared password · Enterprise = per-user via 802.1X/RADIUS · WPA3 > WPA2 · 2.4GHz (range) vs 5/6GHz (speed, less congestion)

## Cloud

IaaS/PaaS/SaaS = how much you manage · Public/Private/Hybrid = who else shares it · NAT gateway = outbound-only internet access · Internet gateway = VPC's door to the internet

## Documentation & DR

RPO = data loss tolerance · RTO = downtime tolerance · MTTR = time to fix · MTBF = time between failures · Cold < Warm < Hot site (readiness & cost) · Active-active (all serve) vs Active-passive (standby)

## Monitoring

SNMPv2c (community string, weak) vs SNMPv3 (authenticated, encrypted) · SIEM = correlated log analysis · Syslog = simple log collection · In-band (rides production network) vs Out-of-band (separate, survives outage)

## DNS records

A (name→IPv4) · AAAA (name→IPv6) · CNAME (alias) · MX (mail) · TXT (SPF/DKIM/DMARC) · NS (authoritative server) · PTR (IP→name, reverse zone)

## Security fundamentals

AAA: Authentication (who) → Authorization (what) → Accounting (logged) · RADIUS/TACACS+ (network AAA) · LDAP (directory) · SAML (web SSO) · CIA triad: Confidentiality, Integrity, Availability

## Attacks

VLAN hopping · MAC flooding · ARP poisoning/spoofing (Layer 2) · DNS poisoning (cache) vs DNS spoofing (forged response) · Evil twin (impersonates SSID) vs rogue AP (just unauthorized) · On-path attack (intercepts traffic)

## Defense

NAC/802.1X/port security = who connects · ACL/URL/content filtering = what traffic passes · Screened subnet = isolates public-facing services · Device hardening = disable unused ports/services, change defaults

## Troubleshooting methodology (7 steps)

1. Identify (gather info, question users, duplicate if possible)
2. Establish a theory (question the obvious; top-down/bottom-up/divide-and-conquer)
3. Test the theory (confirmed → resolve; not confirmed → re-theorize or escalate)
4. Establish a plan of action
5. Implement or escalate
6. Verify functionality + preventive measures
7. Document findings, actions, outcomes, lessons learned

## Troubleshooting symptom map

| Area | Signature symptoms |
|---|---|
| Cabling/interface | CRC errors, runts, giants, error disabled ports, attenuation |
| Network services | STP loops, wrong VLAN, wrong gateway/mask, duplicate IP, DHCP exhaustion |
| Performance | Congestion, latency, jitter, packet loss, wireless interference |
| Tools | ping (reachable?) · traceroute (where broken?) · netstat (connections) · arp (L2 mapping) · nslookup/dig (DNS) |

---

## 🔗 Related Notes

- [[Network+ Codex]]
