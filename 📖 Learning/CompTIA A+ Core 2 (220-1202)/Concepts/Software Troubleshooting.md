---
type: concept
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA A+
difficulty: Intermediate
tags:
  - a-plus-1202
  - troubleshooting
aliases:
  - A+ Software Troubleshooting
publish: true
permalink: comptia-a-core-2-220-1202/software-troubleshooting
---

# <span class="rune">ᛟ</span> Software Troubleshooting

> *All four sub-objectives in this domain are "given a scenario" — this is pure applied judgment, not recall.*

---

## 🎯 Purpose

23% of the exam, covering 3.1-3.4: troubleshooting Windows OS issues, mobile OS/application issues, mobile OS/application *security* issues specifically, and PC security issues. Uses the same [[Hardware and Network Troubleshooting|troubleshooting methodology]] from Core 1, applied to software symptoms.

---

## 🧠 Key Ideas

- Windows OS symptoms (BSOD, boot failures, degraded performance) point to driver, resource, or corruption issues — [[Windows Administration Tools and Command Line|`sfc`, Event Viewer, and Task Manager]] are the primary diagnostic tools
- Mobile app/OS symptoms (crashes, failed installs/updates, connectivity) are usually resource, permission, or connectivity-radio issues — not malware by default
- Mobile OS/app *security* issues are a distinct sub-objective specifically because malicious apps present differently: high network traffic, unauthorized data access, fake warnings
- PC security symptoms overlap with malware behavior but focus on user-visible signs: false AV alerts, browser redirects, altered/missing files
- The four areas are separated deliberately — don't reach for a "security" answer on a plain performance symptom, or vice versa

---

## ⚙️ How It Works

### Windows OS issues

| Symptom | Likely cause |
|---|---|
| BSOD | Driver fault, hardware failure, or corrupted system file |
| Boot issues / no OS found | Corrupted boot files, wrong boot order, failing drive |
| Degraded performance, low memory warnings | Resource exhaustion — check Task Manager/Resource Monitor |
| Services not starting | Dependency failure or corrupted service |
| Frequent shutdowns | Overheating, power issue, or driver fault |
| Slow profile load, time drift | Corrupted user profile; time drift often points to a dying CMOS battery or NTP sync failure |

### Mobile OS and application issues (non-security)

| Symptom | Likely cause |
|---|---|
| App fails to launch/close/update/install | Storage full, incompatible OS version, corrupted app data |
| Slow to respond, random reboots | Resource exhaustion, aging battery, background processes |
| Connectivity issues (Bluetooth/Wi-Fi/NFC) | Radio hardware, settings, or interference |
| Battery life issues | Aging battery, background app activity |
| Screen does not autorotate | Setting disabled, or sensor/digitizer fault |

### Mobile OS and application *security* issues

| Symptom | Likely cause |
|---|---|
| High network traffic, data-usage limit notification | App exfiltrating data or excessive background sync |
| High number of ads, fake security warnings | Adware or malicious app |
| Limited/no internet connectivity | Malicious app interfering with network stack |
| Leaked personal files/data | Unauthorized app access |
| Application from unofficial store, root/jailbreak, developer mode enabled | Root cause context — these conditions make the above symptoms more likely |

### PC security issues

| Symptom | Likely cause |
|---|---|
| Unable to access the network, desktop alerts | Malware interfering with connectivity or displaying fake alerts |
| False antivirus alerts | Rogue/fake AV software (itself a form of malware) |
| Altered/missing/renamed files | Ransomware, unauthorized access, or corruption |
| Random/frequent pop-ups, redirection, certificate warnings | Browser hijack or adware |
| OS update failures | Corruption, malware interference, or insufficient permissions/space |

---

## 💻 Examples

```text
"A Windows PC shows a blue screen after installing a new device driver."
→ Windows OS issue — likely driver fault, boot into Safe Mode and roll
back the driver

"A mobile app repeatedly fails to update despite adequate storage."
→ Mobile OS/application issue — check app cache, compatibility, or
reinstall

"A phone sideloaded from an unofficial store now shows a high number of
ads and unexpected data usage."
→ Mobile OS/application security issue — likely malicious app

"A user's browser shows constant pop-ups and searches redirect to unknown
sites."
→ PC security issue — browser hijack/adware, not the mobile-specific
category
```

---

## 🚨 Common Mistakes

- Treating every mobile symptom as a security issue — most app crashes/slowness are ordinary resource problems, not malware
- Missing that "root access/jailbreak" and "unofficial app store" are *risk factors* in the scenario, not symptoms themselves
- Assuming a false antivirus alert means the real AV found something — it more often means fake/rogue security software is running
- Forgetting time drift on a Windows PC can be a CMOS battery symptom carried over from Core 1, not purely a software issue

---

## 📖 Further Reading

- [CompTIA A+ Core 2 (220-1202) official exam objectives](https://www.comptia.org/certifications/a)

---

## 🔗 Related Notes

- [[Troubleshooting Common Windows OS Issues]] — drill note
- [[Troubleshooting Mobile OS and Application Issues]] — drill note
- [[Troubleshooting Mobile OS and Application Security Issues]] — drill note
- [[Troubleshooting Common PC Security Issues]] — drill note
- [[A+ Core 2 Codex]]
