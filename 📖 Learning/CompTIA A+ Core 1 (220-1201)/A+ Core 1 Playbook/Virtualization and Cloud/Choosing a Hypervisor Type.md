---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA A+
difficulty: Beginner
tags:
  - a-plus-1201
  - virtualization
aliases:
  - Type 1 vs Type 2 Hypervisor
publish: true
permalink: comptia-a-core-1-220-1201/choosing-a-hypervisor-type
---

# <span class="rune">ᚲ</span> Choosing a Hypervisor Type

> *Is there a host OS underneath it, or not? That single question answers this every time.*

---

## 🎯 Problem

A scenario describes a virtualization setup. Identify whether it's using a Type 1 or Type 2 hypervisor.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "runs directly on server hardware, no separate OS installed first" | Type 1 (bare metal) |
| "installed as an application on top of Windows/macOS/Linux" | Type 2 (hosted) |
| "production data center virtualization at scale" | Type 1 |
| "a developer testing a VM on their personal laptop" | Type 2 |
| "best possible performance, minimal overhead" | Type 1 |
| "easiest to set up on an existing desktop for occasional use" | Type 2 |

---

## 🧠 Why This Pattern Works

The presence (or absence) of a general-purpose host OS underneath the hypervisor is the entire distinction. Type 1 talks to hardware directly, which is why it's the standard for production environments where performance and density matter. Type 2 rides on top of an OS the user already has, trading some performance for convenience — ideal for occasional, single-machine use.

---

## 🚨 Common Mistakes

- Assuming "Type 1" means "type of virtualization" in some generic sense rather than the specific bare-metal-vs-hosted distinction
- Picking Type 2 for a data-center/production scenario because it "sounds simpler" — production environments favor Type 1 for performance
- Forgetting a Type 2 hypervisor is still a full application competing for the host OS's own resources

---

## 💡 Wisdom from Mímir

If the scenario mentions installing anything *before* the hypervisor, it's Type 2. If the hypervisor *is* what gets installed on bare hardware, it's Type 1.

---

## 🔗 Related Notes

- [[Virtualization and Cloud Computing]] — full concept writeup
- [[A+ Core 1 Codex]]
