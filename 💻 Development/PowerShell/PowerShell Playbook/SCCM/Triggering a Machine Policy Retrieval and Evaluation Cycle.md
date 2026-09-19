---
type: powershell-pattern
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Intermediate
tags:
  - sccm
  - configuration-manager
  - client-cycles
aliases:
  - Machine Policy Retrieval Cycle
  - CCM Client Cycles PowerShell
publish: true
permalink: powershell/triggering-a-machine-policy-retrieval-and-evaluation-cycle
---

# <span class="rune">ᚲ</span> Triggering a Machine Policy Retrieval and Evaluation Cycle

> *"Just wait for the next scheduled cycle" is a real fix, given enough patience — triggering it manually is the same fix, without the waiting.*

---

## 🎯 Problem

A newly deployed SCCM policy, application, or configuration change hasn't yet shown up on a specific machine, and waiting for the next automatic policy cycle (which can be hours away) isn't acceptable for a ticket that needs resolving now.

More generally, this pattern answers:
> How do I force an SCCM client to immediately check in for new policy and evaluate it, without waiting for its next scheduled cycle?

---

## 🤔 Mental Model

Think of SCCM client cycles as a mail carrier's route — normally running on a fixed schedule, but able to be flagged down early if something urgent needs delivering right now. Triggering the machine policy cycle manually is exactly that: asking the client to check in with the management point immediately, rather than waiting for its next regularly scheduled round.

---

## 🧠 Why This Pattern Works

The SCCM client exposes its scheduled maintenance actions (policy retrieval, evaluation, software updates scan, hardware inventory, and others) as invokable methods on the `CCM_ClientSchedule` WMI class within the `root\ccm` namespace — triggering one via `Invoke-CimMethod` (or the older `Invoke-WmiMethod`) immediately fires that specific cycle rather than waiting for its normal interval, building directly on the [[CIM and WMI|CIM/WMI]] mechanics used throughout this Codex.

---

## 💻 PowerShell Solution

```powershell
# Trigger the Machine Policy Retrieval & Evaluation Cycle immediately
Invoke-CimMethod -Namespace 'root\ccm' -ClassName 'SMS_Client' -MethodName 'TriggerSchedule' `
    -Arguments @{ sScheduleID = '{00000000-0000-0000-0000-000000000021}' }

# Same action on a remote machine
Invoke-CimMethod -ComputerName 'ws042' -Namespace 'root\ccm' -ClassName 'SMS_Client' -MethodName 'TriggerSchedule' `
    -Arguments @{ sScheduleID = '{00000000-0000-0000-0000-000000000021}' }

# Common schedule IDs for reference
$scheduleIds = @{
    'Machine Policy Retrieval & Evaluation Cycle' = '{00000000-0000-0000-0000-000000000021}'
    'Software Update Scan Cycle'                  = '{00000000-0000-0000-0000-000000000113}'
    'Application Deployment Evaluation Cycle'     = '{00000000-0000-0000-0000-000000000121}'
    'Hardware Inventory Cycle'                    = '{00000000-0000-0000-0000-000000000001}'
}
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Identify the correct schedule ID for the desired cycle

```powershell
$scheduleIds['Machine Policy Retrieval & Evaluation Cycle']
```

Each SCCM client action has a fixed, well-documented GUID — the machine policy cycle specifically is `{00000000-0000-0000-0000-000000000021}`, one of the most commonly needed IDs for general troubleshooting.

### Step 2 — Invoke it via CIM against the SMS_Client class

```powershell
Invoke-CimMethod -Namespace 'root\ccm' -ClassName 'SMS_Client' -MethodName 'TriggerSchedule' -Arguments @{sScheduleID = '...'}
```

This calls directly into the local SCCM client's own WMI provider, the same mechanism the Configuration Manager console's own "Run Machine Policy Retrieval Cycle" right-click action uses internally.

### Step 3 — Apply remotely when needed

```powershell
Invoke-CimMethod -ComputerName 'ws042' ...
```

Extends the same trigger to a remote machine, useful for a help desk resolving a ticket without needing physical or console access to that specific machine.

---

## 🚀 Common Use Cases

- Forcing a machine to pick up a newly deployed application or configuration change immediately, rather than waiting for its next scheduled policy cycle
- Troubleshooting a "deployment isn't showing up" ticket by ruling out simple policy timing as the cause
- Combining multiple cycles (policy retrieval, then application evaluation) in sequence to fully refresh a machine's state
- Scripted remediation across a batch of machines that all need an immediate policy refresh after a change

---

## ⚖️ Alternatives

### Triggering multiple related cycles in sequence

```powershell
$cycles = '{00000000-0000-0000-0000-000000000021}', '{00000000-0000-0000-0000-000000000121}'
foreach ($id in $cycles) {
    Invoke-CimMethod -Namespace 'root\ccm' -ClassName 'SMS_Client' -MethodName 'TriggerSchedule' -Arguments @{sScheduleID=$id}
    Start-Sleep -Seconds 10
}
```

Policy retrieval followed by application evaluation, with a short pause between, mirrors what actually needs to happen for a newly deployed application to install promptly.

### Using the Configuration Manager console's "Client Notification" feature (GUI equivalent)

The SCCM console itself offers a right-click "Client Notification > Download Computer Policy" action against a device collection — functionally equivalent for a small number of machines, but the PowerShell approach scales far more easily to scripted, unattended, or bulk scenarios.

---

## ⚠️ Common Mistakes

- Triggering the policy retrieval cycle but not the application evaluation cycle afterward, when the actual goal was getting a *new application* to install — policy retrieval alone only pulls down the new policy; evaluation is what acts on it.
- Using the wrong schedule GUID — they're easy to transpose or mistype, and an incorrect GUID typically fails silently rather than throwing an obviously clear error.
- Assuming this always immediately resolves a deployment issue — if the underlying deployment itself is misconfigured or targeting the wrong collection, forcing the cycle just confirms the client checked in correctly, not that the deployment itself will now succeed.
- Running this against many machines without pacing the requests, potentially generating an unexpected load spike against the management point if triggered against a very large collection all at once.

---

## 📖 Further Reading

- Microsoft Learn: "Configuration Manager client schedule ID reference"

---

## 💡 Wisdom from Mímir

Keep the common schedule GUIDs (machine policy, application evaluation, software update scan, hardware inventory) saved somewhere immediately reachable — these four cover the overwhelming majority of "force this to happen now instead of waiting" SCCM tickets.

---

## 🔗 Related Notes

- [[CIM and WMI]]
- [[Checking SCCM Client Health on a Remote Computer]]
- [[Forcing an Application Deployment to Install Immediately]]
- [[Finding a Device's SCCM Collection Membership]]
