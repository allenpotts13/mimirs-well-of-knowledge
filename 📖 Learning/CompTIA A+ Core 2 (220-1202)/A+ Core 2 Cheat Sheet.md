---
type: cheat-sheet
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA A+
tags:
  - a-plus-1202
  - cheat-sheet
aliases:
  - A+ Core 2 Quick Reference
publish: true
permalink: comptia-a-core-2-220-1202/a-core-2-cheat-sheet
---

# <span class="rune">ᛊ</span> A+ Core 2 Cheat Sheet

> *Last-mile reference. If you can fill in every row of this from memory, you're ready.*

---

## Filesystems

NTFS (Windows) · ReFS (Windows Server) · FAT32/exFAT (cross-platform) · ext4 (Linux) · APFS (macOS)

## Partitioning

GPT = UEFI, >2TB, 128 partitions · MBR = legacy BIOS, 2TB limit, 4 primary partitions

## Windows editions

Home (no domain join, no BitLocker, no RDP host) < Pro (all three) < Pro for Workstations (higher RAM/CPU ceiling) < Enterprise (full management suite)

## MMC snap-ins

| File | Tool |
|---|---|
| `eventvwr.msc` | Event Viewer |
| `diskmgmt.msc` | Disk Management |
| `taskschd.msc` | Task Scheduler |
| `devmgmt.msc` | Device Manager |
| `lusrmgr.msc` | Local Users/Groups |
| `perfmon.msc` | Performance Monitor |
| `gpedit.msc` | Group Policy Editor |
| `regedit.exe` | Registry Editor |
| `msconfig.exe` | System Configuration |
| `msinfo32.exe` | System Information |

## Command-line quick reference

| Task | Command |
|---|---|
| IP config | `ipconfig` |
| Connectivity test | `ping` |
| Path trace | `tracert` / `pathping` |
| DNS query | `nslookup` |
| Active connections | `netstat` |
| Disk errors | `chkdsk` |
| Corrupted system files | `sfc` |
| Partition disk | `diskpart` |
| Apply/report Group Policy | `gpupdate` / `gpresult` |
| Robust file copy | `robocopy` |

## macOS

Time Machine (backup) · FileVault (encryption) · Disk Utility · Terminal · Keychain · Spotlight · `.dmg`(image)/`.pkg`(installer)/`.app`(bundle)

## Linux

`ls` `pwd` `mv` `cp` `rm` `chmod` `chown` `grep` `find` (files) · `su`/`sudo` (admin) · `apt` (Debian) / `dnf` (Fedora) · `ip` `ping` `curl` `dig` (network) · `/etc/passwd` `/etc/shadow` `/etc/hosts` `/etc/fstab`

## Security measures

Physical: badges, biometrics, cameras, locks · Logical: least privilege, Zero Trust, MFA, ACLs, SSO, MDM, DLP · MFA = factors from **different** categories (know/have/are)

## Malware types

Virus · Trojan · Rootkit (hides, privileged) · Ransomware (encrypts+demands) · Spyware (watches) · Keylogger · Cryptominer · Adware/PUP · Fileless (memory-only) · Stalkerware

## SOHO malware removal (10 steps, in order)

1. Investigate/verify → 2. Quarantine → 3. Disable System Restore (Home) → 4. Remediate → 5. Update anti-malware → 6. Scan/remove (safe mode) → 7. Reimage if needed → 8. Schedule scans/updates → 9. Re-enable System Restore + restore point → 10. Educate user

## Social engineering

Phishing (email) · Vishing (voice) · Smishing (SMS) · QR phishing · Spear phishing (targeted person) · Whaling (executive) · Shoulder surfing · Tailgating · Impersonation · Dumpster diving

## Wireless security ranking

WPA3 > WPA2 (AES) > WPA (TKIP) > WEP (never correct) · RADIUS/TACACS+ (network AAA) · Kerberos (domain tickets)

## Data destruction

Physical (drilling/shredding/degaussing/incineration) = irreversible · Standard format ≠ secure erase · Outsourcing needs a certificate of destruction

## Backup types

Full (slow backup, fast restore) · Incremental (fast backup, slow restore) · Differential (middle ground) · Synthetic full (combines without re-reading source) · **3-2-1 rule**: 3 copies, 2 media types, 1 offsite · GFS rotation

## Change management

Standard (pre-approved) · Normal (needs approval) · Emergency (urgent, documented after) · Always needs: rollback plan, backup plan, sandbox testing, end-user acceptance

## Remote access

RDP (Windows GUI) · VNC (cross-platform GUI) · SSH (secure CLI) · VPN (secure tunnel) · RMM (fleet management) · WinRM (Windows remote mgmt)

## Scripting file types

`.bat` batch · `.ps1` PowerShell · `.vbs` VBScript · `.sh` shell · `.js` JavaScript · `.py` Python

## Safety

ESD strap/mat/antistatic bag = protects **components** · Disconnect power, lifting technique, fire safety, goggles = protects **you** · UPS = keeps running through outage · Surge suppressor = spike protection only

---

## 🔗 Related Notes

- [[A+ Core 2 Codex]]
