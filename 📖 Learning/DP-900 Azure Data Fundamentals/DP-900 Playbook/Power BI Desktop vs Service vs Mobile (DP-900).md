---
type: exam-scenario
status: active
created: 2026-09-10
updated: 2026-09-10
technology: Azure
difficulty: Beginner
tags:
  - dp-900
  - power-bi
aliases:
  - Power BI Apps
---

# 🧩 Power BI Desktop vs Service vs Mobile (DP-900)

> *Same product family, three apps with non-overlapping jobs — the dashboard question is the classic trap.*

---

## 🎯 Problem

A scenario asks where a specific Power BI artifact (dataset, report, dashboard) is built, published, or viewed, and expects you to name the right app.

---

## 🤔 Mental Model

| App | Job | Can build dashboards? |
|---|---|---|
| **Power BI Desktop** | Author reports & data models (Windows app) | ❌ No |
| **Power BI Service** | Publish, share, collaborate, pin tiles into dashboards (cloud, app.powerbi.com) | ✅ Yes — dashboards live here only |
| **Power BI Mobile** | View reports/dashboards on phone/tablet | ❌ View only |
| **Power BI Report Builder** | Build pixel-perfect "paginated reports" | ❌ No |

---

## 🧠 Why This Pattern Works

Desktop is the authoring tool — it builds the data model and report pages, but has no concept of "pinning tiles from multiple reports into one canvas," because that's a collaboration/sharing feature that only makes sense once content is published to the cloud. That's why dashboards are Service-exclusive: they're a *aggregation* feature across already-published reports, not an authoring feature.

---

## 🔑 Decision Table

| Scenario signal | Answer |
|---|---|
| "Build a report and data model from a CSV file" | Power BI Desktop |
| "Combine tiles from three different reports onto one screen" | Power BI Service — build a Dashboard |
| "Check sales numbers on my phone during a meeting" | Power BI Mobile |
| "Print a pixel-perfect invoice-style report" | Power BI Report Builder (paginated report) |
| "Share a curated set of reports with the sales team" | Power BI Service — publish an App |

---

## 🚨 Common Mistakes

- Saying a dashboard can be built in Desktop — it cannot, dashboards are Service-only
- Confusing "Report" (pages of visuals from one dataset) with "Dashboard" (tiles pinned from possibly many reports)

---

## 💡 Wisdom from Mímir

Whenever a question mentions "pinning" or "tiles," the answer is Dashboard, and the location is always the Service.

---

## 🔗 Related Notes

- [[Analytics Workloads on Azure]] — full concept writeup
- [[DP-900 Codex]]
