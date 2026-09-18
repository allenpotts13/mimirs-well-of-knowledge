---
type: cheat-sheet
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA A+
tags:
  - a-plus-1201
  - cheat-sheet
aliases:
  - A+ Core 1 Quick Reference
publish: true
permalink: comptia-a-core-1-220-1201/a-core-1-cheat-sheet
---

# <span class="rune">ᛊ</span> A+ Core 1 Cheat Sheet

> *Last-mile reference. If you can fill in every row of this from memory, you're ready.*

---

## Troubleshooting methodology (memorize the order)

1. Identify the problem
2. Establish a theory of probable cause (question the obvious)
3. Test the theory
4. Establish a plan of action, implement the solution
5. Verify full system functionality
6. Document findings, actions, and outcomes

## Common ports

| Port | Protocol | Port | Protocol |
|---|---|---|---|
| 20-21 | FTP | 143 | IMAP |
| 22 | SSH | 389 | LDAP |
| 23 | Telnet | 443 | HTTPS |
| 25 | SMTP | 445 | SMB/CIFS |
| 53 | DNS | 3389 | RDP |
| 67/68 | DHCP | 80 | HTTP |
| 110 | POP3 | 137-139 | NetBIOS |

## TCP vs UDP

- **TCP** = connection-oriented, guaranteed, ordered (HTTP, FTP, SSH, email)
- **UDP** = connectionless, fast, no guarantee (DNS, streaming, VoIP)

## Wireless bands

- **2.4GHz** = longer range, more interference, slower
- **5GHz/6GHz** = shorter range, less interference, faster

## IPv4 addressing

| Term | Meaning |
|---|---|
| Private | 10.x, 172.16-31.x, 192.168.x — not routable on internet |
| Public | Globally routable |
| APIPA | 169.254.x.x — DHCP unreachable, not a working address |
| Static / Dynamic | Manual / DHCP-assigned |

## Networking hardware

Router (between networks) → Switch (within a network) → Access point (wired→wireless) → Firewall (filters traffic) → Patch panel (passive cable termination)

## Cable/connector quick ID

| Connector | Family |
|---|---|
| RJ45 | Ethernet |
| RJ11 | Phone/DSL |
| ST, SC, LC | Fiber |
| HDMI, DP, DVI, VGA | Video |
| SATA, eSATA | Storage |

## RAM

- **DIMM** = desktop/server, **SODIMM** = laptop
- **ECC** = error-correcting, servers/workstations; **non-ECC** = consumer desktops
- DDR generations are **not** cross-compatible

## Storage & RAID

| RAID | Tolerance | Usable capacity |
|---|---|---|
| 0 | None | 100% |
| 1 | 1 drive | 50% |
| 5 | 1 drive | (n-1)/n |
| 6 | 2 drives | (n-2)/n |
| 10 | Multiple (mirrored+striped) | 50% |

NVMe (PCIe) >> SATA SSD > HDD for speed.

## Motherboard/PSU

- Form factor (largest→smallest): **ATX → microATX → ITX**
- CPU socket must match vendor (AMD/Intel) and generation
- PSU: check **wattage headroom**, **12V rail capacity**, input voltage (110-120V vs 220-240V)
- TPM must be enabled in firmware before BitLocker works

## Virtualization & cloud

- **Type 1 hypervisor** = bare metal, no host OS (production/data center)
- **Type 2 hypervisor** = runs on top of a host OS (dev/test, single machine)
- **IaaS/PaaS/SaaS** — you manage less as you move IaaS → PaaS → SaaS
- **Elasticity** = automatic scaling; **metered utilization** = billed by actual use (including egress)

## Troubleshooting symptom map

| Area | Signature symptoms |
|---|---|
| Motherboard/RAM/CPU/power | POST beeps, no power, burning smell, capacitor swelling, random shutdown |
| Drive/RAID | Grinding/clicking, S.M.A.R.T. failure, array missing |
| Video/display | Dim/fuzzy image, dead pixels, burn-in, wrong input source |
| Mobile devices | Swollen battery (safety!), liquid damage, digitizer issues |
| Network | Intermittent (auth/physical) vs. slow (congestion/interference) — different trees |
| Printers | Faded = consumables; lines/speckling = drum/nozzle; jams = rollers; frozen queue = spooler |

## Printer maintenance by type

| Type | Maintenance |
|---|---|
| Laser | Replace toner, maintenance kit, calibrate, clean |
| Inkjet | Clean printheads, replace cartridges, calibrate |
| Thermal | Replace paper, clean heating element |
| Impact | Replace ribbon, printhead, paper |

---

## 🔗 Related Notes

- [[A+ Core 1 Codex]]
