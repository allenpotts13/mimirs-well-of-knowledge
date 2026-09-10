---
type: exam-scenario
status: active
created: 2026-09-10
updated: 2026-09-10
technology: Azure
difficulty: Beginner
tags:
  - dp-900
  - analytics
  - stream-analytics
  - data-factory
aliases:
  - Batch vs Streaming
---

# 🧩 Batch vs Streaming - Which Azure Service Fits

> *Latency requirement in the scenario tells you which family of services to reach for.*

---

## 🎯 Problem

A scenario describes how urgently data needs to be processed after it's generated. Decide whether it's a batch or streaming workload, then pick the matching Azure service.

---

## 🤔 Mental Model

| | Batch | Streaming |
|---|---|---|
| Data arrives | In scheduled chunks | Continuously |
| Processed | On a schedule (e.g. nightly) | As it arrives, near real-time |
| Latency tolerance | Minutes to hours | Milliseconds to seconds |
| Typical trigger word | "nightly," "scheduled," "periodic" | "real-time," "as it happens," "live" |

---

## 🧠 Why This Pattern Works

Batch and streaming are two different engineering trade-offs for the same underlying goal (move + transform data). Batch optimizes for throughput and simplicity; streaming optimizes for latency at the cost of more complex infrastructure. Azure gives you a distinct service family for each because the underlying architecture is genuinely different, not just a config toggle.

---

## 🔑 Decision Table

| Scenario signal | Service |
|---|---|
| "Copy/transform data on a schedule between stores" | Azure Data Factory (or Synapse Pipelines) |
| "Ingest millions of events per second" (the pipe, not the processing) | Azure Event Hubs |
| "Bi-directional communication with IoT devices" | Azure IoT Hub |
| "Query/aggregate a live stream in near real-time with SQL-like syntax" | Azure Stream Analytics |
| "Process a continuous stream with custom Spark code" | Azure Databricks (structured streaming) |

---

## 🚨 Common Mistakes

- Answering "Data Factory" for a real-time processing question just because it's the most familiar name — Data Factory orchestrates and moves data, it doesn't do sub-second stream processing
- Confusing Event Hubs (the ingestion pipe) with Stream Analytics (the thing that queries/processes what's flowing through the pipe) — they're usually used together, not as alternatives

---

## 💡 Wisdom from Mímir

Ingestion and processing are two different jobs even in a streaming pipeline. Event Hubs/IoT Hub get the data in the door; Stream Analytics (or Databricks) is what actually does something with it in real time.

---

## 🔗 Related Notes

- [[Analytics Workloads on Azure]] — full concept writeup
- [[Data Factory vs Stream Analytics vs Synapse]]
- [[DP-900 Codex]]
