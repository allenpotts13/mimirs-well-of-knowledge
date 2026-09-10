---
type: concept
status: active
created: 2026-09-10
updated: 2026-09-10
technology: Power BI
difficulty: Beginner
tags:
  - power-bi
  - fundamentals
aliases:
  - Power BI Apps
---

# 📚 Power BI Desktop vs Service vs Mobile

> Three different applications, each responsible for one stage of a report's life — building it, publishing it, and viewing it on the go.

---

## 🎯 Purpose

Knowing which app does which job saves a lot of "why can't I do X here" confusion — most of it comes from trying to do a Service-only task in Desktop, or vice versa.

---

## 🧠 Key Ideas

- **Power BI Desktop** — free Windows application for authoring: connecting to data, modeling, writing DAX, building report pages.
- **Power BI Service** (app.powerbi.com) — the cloud platform for publishing, sharing, scheduling refreshes, and collaboration. Dashboards live here only.
- **Power BI Mobile** — iOS/Android/Windows app for viewing published reports and dashboards, with some light interaction (filtering, drill-through, alerts).
- **Power BI Report Builder** — a separate tool for paginated (pixel-perfect, printable) reports, distinct from the interactive reports built in Desktop.

---

## ⚙️ How It Works

```text
Desktop  →  build the .pbix file (data model + report pages)
   ↓ Publish
Service  →  host, share, refresh, collaborate, build dashboards
   ↓ View
Mobile   →  consume on a phone/tablet, light interaction only
```

Desktop is where the real editing happens. Once published, most day-to-day changes to visuals still require going back to Desktop, republishing — although the Service does allow some direct editing depending on licensing and settings.

---

## 💻 Examples

```text
"Connect to a SQL database, build a star schema, write DAX measures,
design report pages."
→ Power BI Desktop

"Schedule this dataset to refresh every morning at 6am and share it
with the finance team."
→ Power BI Service

"Check yesterday's sales numbers from my phone before a meeting."
→ Power BI Mobile

"Build a fixed-layout financial statement that prints exactly the same
every time."
→ Power BI Report Builder (paginated reports)
```

---

## 🚀 Real World Applications

- Deciding where a requested change actually needs to happen
- Planning a deployment: build in Desktop, publish to Service, distribute via App, consume on Mobile
- Explaining why a dashboard can't be built without first publishing a report

---

## ⚖️ Advantages

- Clear separation between authoring and consumption keeps the underlying data model consistent across everyone viewing it.
- Mobile apps give real-time access without needing a laptop.

---

## ⚠️ Limitations

- Desktop is Windows-only; Mac users need a VM or the Service's web-based editing (limited compared to Desktop).
- Mobile can't build or substantially edit reports — it's a consumption tool.

---

## 🚨 Common Mistakes

- Trying to build a dashboard in Desktop — dashboards are Service-only.
- Forgetting that changes made in Desktop aren't live until republished to the Service.
- Assuming Mobile supports the same editing capabilities as Desktop or the Service.

---

## 📖 Further Reading

Documentation

Books

Videos

Articles

---

## 💡 Wisdom from Mímir

If a request sounds like "add this new visual" or "change this measure," it's a Desktop job, republished afterward. If it sounds like "share this with a new group" or "refresh this automatically," it's a Service job. Sorting a request into one of those two buckets first saves a lot of back-and-forth.

---

## 🔗 Related Notes

- [[Power BI Building Blocks]]
- [[Power BI Gateway]]
