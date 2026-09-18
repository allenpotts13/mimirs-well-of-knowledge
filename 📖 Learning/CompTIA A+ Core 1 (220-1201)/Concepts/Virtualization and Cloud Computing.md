---
type: concept
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA A+
difficulty: Beginner
tags:
  - a-plus-1201
  - virtualization
  - cloud-computing
aliases:
  - A+ Virtualization
  - A+ Cloud Computing
publish: true
permalink: comptia-a-core-1-220-1201/virtualization-and-cloud-computing
---

# <span class="rune">ᛟ</span> Virtualization and Cloud Computing

> *The smallest domain on Core 1 — 11% — but it's the conceptual bridge into everything AZ-900 covers in depth.*

---

## 🎯 Purpose

11% of the exam, covering 4.1-4.2: virtualization concepts (hypervisors, VM purposes, containers) and cloud computing concepts (deployment models, service models, cloud characteristics). Lighter than the rest of Core 1, but a favorite spot for "compare and contrast" style questions.

---

## 🧠 Key Ideas

- A **hypervisor** is what makes virtualization possible; **Type 1** runs directly on hardware (bare metal), **Type 2** runs on top of a host OS
- VMs get used for sandboxing, test/dev environments, legacy software/OS support, and cross-platform application virtualization
- **Containers** virtualize at the OS level (share the host kernel) rather than virtualizing full hardware like a VM does
- Cloud deployment models (private/public/hybrid/community) and service models (IaaS/PaaS/SaaS) are the same vocabulary AZ-900 covers — Core 1 tests it at a lighter, vendor-neutral level
- Cloud characteristics (elasticity, metered utilization, multitenancy) describe *why* cloud behaves differently than owned hardware

---

## ⚙️ How It Works

### Hypervisor types

| Type | Runs on | Example use | Overhead |
|---|---|---|---|
| Type 1 (bare metal) | Directly on hardware | Data centers, production servers | Lower |
| Type 2 (hosted) | On top of a host OS | Desktop virtualization, dev/test | Higher |

### Why virtualize

| Purpose | Scenario |
|---|---|
| Sandbox | Isolate untrusted software/testing |
| Test development | Spin up/tear down environments quickly |
| Legacy software/OS | Run an app that needs an old OS without keeping old hardware |
| Cross-platform virtualization | Run software built for a different OS |
| Desktop virtualization (VDI) | Centralize desktops, deliver remotely |

**Containers** differ from VMs by sharing the host OS kernel instead of virtualizing hardware — lighter weight, faster to start, less isolated than a full VM.

### Virtualization requirements

Security, network, and storage all need planning *before* deploying VMs — a VM is still a full system that needs patching, network segmentation, and storage allocation like physical hardware.

### Cloud deployment models

| Model | Description |
|---|---|
| Private | Dedicated to one organization |
| Public | Third-party, shared infrastructure |
| Hybrid | Public + private combined |
| Community | Shared by organizations with common concerns (e.g., regulatory) |

### Cloud service models

| Model | You manage | Provider manages |
|---|---|---|
| IaaS | OS, runtime, apps, data | Hardware, virtualization, networking |
| PaaS | Apps, data | OS, runtime, infrastructure |
| SaaS | Data, config | Everything else |

### Cloud characteristics

| Characteristic | Meaning |
|---|---|
| Shared vs. dedicated resources | Multi-tenant pool vs. resources reserved for one customer |
| Metered utilization (ingress/egress) | Billed by actual usage, including data transfer direction |
| Elasticity | Automatic scale up/down with demand |
| Availability | Uptime guarantees |
| File synchronization | Keeping data consistent across devices/locations |
| Multitenancy | Multiple customers share underlying infrastructure, isolated logically |

---

## 💻 Examples

```text
"A company runs its production database directly on a hypervisor with no
underlying host OS."
→ Type 1 hypervisor

"A developer runs a Windows VM on their macOS laptop to test software."
→ Type 2 hypervisor

"An old accounting application only runs on Windows XP, and the company
can't risk keeping that OS on live hardware."
→ Virtualize it — legacy software/OS use case

"A company wants an app deployment platform where they manage only the
application and its data."
→ PaaS

"A cloud bill spikes because of unexpectedly high data leaving the
provider's network."
→ Metered utilization — specifically egress cost
"
```

---

## 🚨 Common Mistakes

- Assuming containers and VMs are the same thing — containers share the host kernel, VMs virtualize full hardware
- Mixing up Type 1 (bare metal, no host OS) and Type 2 (runs inside a host OS) — a common direct exam question
- Confusing "hybrid cloud" (public + private together) with "community cloud" (shared among similar orgs, not necessarily involving a private component)
- Forgetting egress (data leaving) is often billed differently than ingress (data entering) a cloud service

---

## 📖 Further Reading

- [CompTIA A+ Core 1 (220-1201) official exam objectives](https://www.comptia.org/certifications/a)

---

## 🔗 Related Notes

- [[Choosing a Hypervisor Type]] — drill note
- [[Matching a Scenario to a Cloud Characteristic]] — drill note
- [[Cloud Concepts]] — the AZ-900 deep dive on this same vocabulary
- [[A+ Core 1 Codex]]
