---
type: concept
status: published
created: 2026-09-10
updated: 2026-09-10
technology: Power BI
difficulty: Intermediate
tags:
  - power-bi
  - architecture
  - refresh
aliases:
  - On-Premises Data Gateway
publish: true
permalink: power-bi/power-bi-gateway
---

# <span class="rune">ᛟ</span> Power BI Gateway

> A bridge that lets the cloud-based Power BI Service reach data sources that live behind a firewall, on-premises.

---

## 🎯 Purpose

The Power BI Service runs in the cloud, but plenty of real data — an on-prem SQL Server, a local file share, an internal API — doesn't. A gateway is what makes scheduled refresh and DirectQuery possible against that kind of source.

---

## 🧠 Key Ideas

- Installed as software on a machine inside the local network, with access to the data source.
- **Personal gateway** — single user, can't be shared, simpler setup, no high availability.
- **Standard (enterprise) gateway** — shared across an organization, supports multiple data sources and users, can be clustered for high availability.
- Acts purely as a secure relay — credentials and queries pass through it, but it doesn't store report or dataset content itself.
- Required for both scheduled refresh of Import-mode datasets and live queries against DirectQuery sources that are on-prem.

---

## ⚙️ How It Works

```text
Power BI Service (cloud)
        ↓ encrypted request
Gateway (installed on-prem, behind the firewall)
        ↓ local network access
On-prem data source (SQL Server, file share, etc.)
```

The gateway maintains an outbound-only connection to the Service, so no inbound firewall rules need to be opened — it polls for pending refresh/query requests rather than being reachable from outside.

---

## 💻 Examples

```text
"Refresh an Import-mode dataset nightly from an on-prem SQL Server."
→ Standard gateway, scheduled refresh

"DirectQuery a source system that lives behind the corporate firewall."
→ Standard gateway, always-on for live queries

"One analyst refreshing their own personal reports from their local machine."
→ Personal gateway (not shareable with others)
```

---

## 🚀 Real World Applications

- Connecting cloud-hosted Power BI reports to an internal, on-prem database
- Enabling scheduled refresh for datasets built on local files or on-prem systems
- Supporting DirectQuery against an on-prem source in near real time

---

## ⚖️ Advantages

- No need to open inbound firewall ports — the gateway only makes outbound connections.
- Enterprise gateways can be clustered for redundancy and load balancing.
- Centralizes credential management for data sources instead of scattering credentials across individual reports.

---

## ⚠️ Limitations

- A single point of failure if not clustered — gateway downtime blocks refreshes and DirectQuery.
- Requires a dedicated, always-on machine to host it.
- Personal gateways can't be managed or shared by an admin, making them unsuitable for team use.

---

## 🚨 Common Mistakes

- Setting up a personal gateway for something that needs to be shared across a team — it silently blocks anyone else from managing or reusing it.
- Not clustering the gateway for a business-critical refresh schedule, creating an unplanned single point of failure.
- Forgetting the gateway needs to stay running and updated — an offline gateway machine silently breaks every refresh depending on it.

---

## 📖 Further Reading

Documentation

Books

Videos

Articles

---

## 💡 Wisdom from Mímir

Treat the gateway machine like production infrastructure, not a random desktop someone forgot about. When a scheduled refresh mysteriously stops working, checking whether the gateway machine is even turned on is worth doing before anything else.

---

## 🔗 Related Notes

- [[Storage Modes - Import vs DirectQuery vs Live Connection]]
- [[Dataflows]]
