---
type: exam-scenario
status: published
created: 2026-09-10
updated: 2026-09-10
technology: Azure
difficulty: Beginner
tags:
  - dp-900
  - blob-storage
  - azure-storage
aliases:
  - Blob Access Tiers
publish: true
---

# <span class="rune">ᚲ</span> Picking a Blob Storage Access Tier

> *Cost and retrieval speed move in opposite directions — the scenario tells you which one matters more.*

---

## 🎯 Problem

A scenario describes how often data is accessed and how quickly it must be retrieved. Pick Hot, Cool, Cold, or Archive.

---

## 🤔 Mental Model

```
Hot ────────── Cool ────────── Cold ────────── Archive
frequent          infrequent        rare            almost never
low retrieval $   higher $          higher $         highest $ (hours to rehydrate)
highest storage $ lower $           lower $          lowest storage $
```

Storage cost and retrieval cost trade off in opposite directions as you move right.

---

## 🧠 Why This Pattern Works

Azure prices tiers assuming you know your access pattern in advance: pay more to store, less to retrieve (Hot) vs. pay less to store, more to retrieve (Archive). The exam gives you the access pattern in the scenario text — "accessed daily" vs. "accessed once a year for compliance."

---

## 🔑 Decision Table

| Scenario signal | Tier |
|---|---|
| Actively served, accessed constantly (e.g. website images) | Hot |
| Backups accessed occasionally, ≥30-day retention | Cool |
| Rarely touched, ≥90-day retention, cost-sensitive but need faster-than-Archive access | Cold |
| Compliance/legal archive, retrieval delay (hours) acceptable, cost is top priority | Archive |

---

## 🚨 Common Mistakes

- Picking Archive whenever cost is mentioned, without checking whether the scenario requires *immediate* retrieval (Archive data must be rehydrated first — that can take hours)
- Forgetting Cold tier exists as a middle option between Cool and Archive
- Applying a tier to Table/Queue/File storage — access tiers are a Blob storage feature specifically

---

## 💡 Wisdom from Mímir

Two questions answer this every time: "how often is it read?" and "how fast does it need to come back when it is?" Archive wins only when the answer to the second question is "it's fine to wait."

---

## 🔗 Related Notes

- [[Non-Relational Data on Azure]] — full concept writeup
- [[DP-900 Codex]]
