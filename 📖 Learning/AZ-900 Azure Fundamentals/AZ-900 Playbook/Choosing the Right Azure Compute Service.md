---
type: exam-scenario
status: published
created: 2026-09-10
updated: 2026-09-10
technology: Azure
difficulty: Beginner
tags:
  - az-900
  - compute
aliases:
  - Azure Compute Decision
publish: true
---

# 🧩 Choosing the Right Azure Compute Service

> *Six compute services, one spectrum: how much of the stack do you want to manage versus hand off?*

---

## 🎯 Problem

A scenario describes a workload's requirements (control, scale, containers, event-driven) and asks which Azure compute service fits.

---

## 🤔 Mental Model

```
VMs → VM Scale Sets → AKS → App Service / Container Instances → Functions
(most control,                                                    (least control,
 most management)                                                  pay-per-execution)
```

---

## 🔑 Decision Table

| Scenario signal | Service |
|---|---|
| "Full control of the OS," "lift and shift a legacy app" | Virtual Machines |
| "Identical VMs that auto-scale with demand" | Virtual Machine Scale Sets |
| "Run a single container quickly, no orchestration needed" | Container Instances (ACI) |
| "Orchestrate many containers, manage scaling/networking between them" | Azure Kubernetes Service (AKS) |
| "Host a web app/API without managing servers" | App Service |
| "Run a small piece of code only when triggered, pay per execution" | Azure Functions |
| "Deliver virtual desktops/apps to remote users" | Azure Virtual Desktop |

---

## 🧠 Why This Pattern Works

Each service exists because different workloads want a different point on the control-vs-convenience trade-off. A legacy app that needs a specific OS patch level wants VMs; a small notification-sending script that runs 10 times a day wants Functions, because paying for an always-on VM for that would be wasteful.

---

## 🚨 Common Mistakes

- Reaching for AKS when the scenario only needs one container (ACI is simpler and cheaper for that case)
- Picking Functions for a long-running, stateful process — Functions are built for short-lived, event-triggered executions
- Forgetting App Service exists as the PaaS middle ground when a scenario says "web app" without mentioning containers or infrastructure control

---

## 💡 Wisdom from Mímir

Ask: does the scenario care about the OS at all? If yes, VMs/Scale Sets. If it cares about containers specifically, ACI or AKS depending on orchestration complexity. If it's "just run my web code," App Service. If it's "run this tiny thing occasionally," Functions.

---

## 🔗 Related Notes

- [[Core Azure Architecture and Services]] — full concept writeup
- [[AZ-900 Codex]]
