---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Security+
difficulty: Intermediate
tags:
  - security-plus
  - security-operations
  - incident-response
aliases:
  - Investigation Data Source Choice
publish: true
permalink: comptia-security-sy0-701/using-data-sources-to-support-an-investigation
---

# <span class="rune">ᚲ</span> Using Data Sources to Support an Investigation

> *No single log tells the whole story — an investigation is always a correlation exercise across sources.*

---

## 🎯 Problem

A scenario describes an investigation need. Identify the correct data source to consult.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "confirm whether traffic to a malicious IP was blocked or allowed" | Firewall logs |
| "determine what a specific application did at a given time" | Application logs |
| "check what happened on a specific workstation or server" | Endpoint logs |
| "review OS-level security events like login attempts" | OS-specific security logs |
| "confirm whether an intrusion detection/prevention system flagged the activity" | IPS/IDS logs |
| "trace how traffic moved across the network" | Network logs |
| "determine when and by whom a file was created or modified" | Metadata |
| "get a full record of every packet exchanged during the incident" | Packet captures |
| "see whether a known vulnerability could explain how the attacker got in" | Vulnerability scans |
| "get a high-level, pre-aggregated view of current security posture" | Dashboards |

---

## 🧠 Why This Pattern Works

Investigation data sources split into **log data** (firewall, application, endpoint, OS, IPS/IDS, network — each capturing a different layer of activity) and **supplementary data** (vulnerability scans explaining *how*, packet captures providing raw detail, dashboards/automated reports providing summarized context). A thorough investigation almost always correlates multiple sources — a scenario naming one specific question ("was it blocked," "what did the app do," "who touched this file") points to exactly one log type as the fastest answer.

---

## 🚨 Common Mistakes

- Relying on a single log source when the investigation actually requires correlating several (e.g., firewall + endpoint + network logs together)
- Confusing metadata (data about the data — timestamps, authorship) with the file's actual content
- Overlooking packet captures as a valid data source when deep protocol-level detail is genuinely needed
- Treating dashboards as a primary evidence source rather than a summarized view built from underlying logs

---

## 💡 Wisdom from Mímir

Ask "what specific question is this investigation trying to answer?" — that question names the exact log or data source needed, rather than reaching for "check the logs" generically.

---

## 🔗 Related Notes

- [[Automation, Incident Response, and Digital Forensics]] — full concept writeup
- [[Security+ Codex]]
