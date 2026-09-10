---
type: exam-scenario
status: active
created: 2026-09-10
updated: 2026-09-10
technology: Azure
difficulty: Beginner
tags:
  - az-900
  - cloud-concepts
  - service-models
aliases:
  - Service Model Recognition
---

# 🧩 IaaS vs PaaS vs SaaS - Spotting the Service Model

> *What are you responsible for managing in this scenario? That's the whole question.*

---

## 🎯 Problem

A scenario describes a service or how much a team manages themselves. Classify it as IaaS, PaaS, or SaaS.

---

## 🤔 Mental Model

```
IaaS ──────────────── PaaS ──────────────── SaaS
you manage most         you manage just        provider manages
(OS, runtime,            app + data              almost everything
 middleware, app, data)                          (you manage data/config only)
```

---

## 🔑 Decision Table

| Scenario signal | Model |
|---|---|
| "Team patches and configures the OS themselves" | IaaS |
| "Team deploys code, provider handles OS/runtime/scaling" | PaaS |
| "Team just uses the finished application, e.g. logs into a web-based email client" | SaaS |
| Azure Virtual Machines | IaaS |
| Azure App Service, Azure SQL Database | PaaS |
| Microsoft 365, Outlook.com | SaaS |

---

## 🧠 Why This Pattern Works

Each model draws the management line at a different layer of the stack. The exam gives you a clue about *who does the work* (patching, scaling, configuring) — trace that clue to the layer it describes, and that layer tells you the model.

---

## 🚨 Common Mistakes

- Assuming any Azure-hosted service must be PaaS — VMs hosted in Azure are still IaaS, because you manage the OS
- Forgetting that PaaS still requires you to manage your application code and data — it's not "zero management," it's "zero infrastructure management"

---

## 💡 Wisdom from Mímir

If the scenario mentions patching an OS, it's IaaS. If it mentions deploying code without touching a server, it's PaaS. If it mentions just using a finished product, it's SaaS. The verb is the tell.

---

## 🔗 Related Notes

- [[Cloud Concepts]] — full concept writeup
- [[AZ-900 Codex]]
