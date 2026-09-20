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
  - client-health
aliases:
  - CCMExec Health Check
  - SCCM Client Diagnostics
publish: true
permalink: powershell/checking-sccm-client-health-on-a-remote-computer
---

# <span class="rune">ᚲ</span> Checking SCCM Client Health on a Remote Computer

> *A machine invisible to SCCM isn't necessarily broken — but a broken SCCM client and a genuinely offline machine look identical from the console, and only checking the client directly tells them apart.*

---

## 🎯 Problem

A machine isn't showing up correctly in the SCCM console, isn't receiving deployments, or is reporting stale inventory data. Determine whether the SCCM client itself is healthy before assuming a deployment or policy configuration problem.

More generally, this pattern answers:
> Is the SCCM client on this machine actually running and healthy, before troubleshooting anything further up the chain?

---

## 🤔 Mental Model

Treat the SCCM client (the `CcmExec` service and its supporting WMI namespace) as a courier who has to actually be at work before any package can be delivered — a perfectly correct deployment targeting the right collection accomplishes nothing if the courier itself isn't running. Checking client health first rules out the simplest, most common explanation before investigating anything more complex further up the SCCM hierarchy.

---

## 🧠 Why This Pattern Works

The SCCM client runs as the `CcmExec` Windows service, as covered generally in [[Process and Service Cmdlets]] — if it's stopped, crashed, or in a bad state, the machine won't process policy, run deployments, or report inventory regardless of how correctly everything is configured on the SCCM server side. The client also exposes its own self-diagnostic data through WMI (`root\ccm\ClientSDK`), and Microsoft provides `CCMEval` (Configuration Manager Client Health Evaluation) specifically to detect and often automatically repair common client-side corruption.

---

## 💻 PowerShell Solution

```powershell
# Check the client service itself — the most basic first check
Get-Service -Name CcmExec -ComputerName 'ws042'

# Full client health check via CCMEval (also attempts automatic repair of common issues)
Invoke-Command -ComputerName 'ws042' -ScriptBlock {
    Start-Process -FilePath "$env:WinDir\CCM\CcmEval.exe" -Wait
}

# Check last successful policy retrieval and heartbeat via WMI
Invoke-Command -ComputerName 'ws042' -ScriptBlock {
    Get-CimInstance -Namespace 'root\ccm' -ClassName 'SMS_Client' | Select-Object ClientVersion
    Get-CimInstance -Namespace 'root\ccm\policy\machine\actualconfig' -ClassName 'CCM_Client' -ErrorAction SilentlyContinue
}

# Restart the client service as a first remediation attempt
Invoke-Command -ComputerName 'ws042' -ScriptBlock {
    Restart-Service -Name CcmExec -Force
}
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Confirm the client service is even running

```powershell
Get-Service -Name CcmExec -ComputerName 'ws042'
```

The most basic and fastest check — if `CcmExec` isn't running at all, nothing else about SCCM will function on that machine regardless of any other configuration.

### Step 2 — Run CCMEval for a comprehensive health check and auto-repair attempt

```powershell
Start-Process -FilePath "$env:WinDir\CCM\CcmEval.exe" -Wait
```

Microsoft's own client health evaluation tool checks for common corruption (missing WMI namespaces, broken service dependencies, certificate issues) and automatically attempts to repair many of them without further manual intervention.

### Step 3 — If issues persist, restart the client service directly

```powershell
Restart-Service -Name CcmExec -Force
```

A straightforward [[Restarting a Hung Windows Service|service restart]] resolves a meaningful share of transient client issues, following the same escalation logic used for any hung Windows service.

---

## 🚀 Common Use Cases

- First-response triage when a machine isn't appearing correctly in the SCCM console or isn't receiving deployments
- Confirming client health before escalating a deployment failure investigation to the SCCM server/site infrastructure side
- Scripted health checks across a batch of machines suspected of client-side problems after a fleet-wide issue
- Post-remediation verification that a repaired client is genuinely healthy again

---

## ⚖️ Alternatives

### Fleet-wide client service health check

```powershell
$computers = 'ws001','ws002','ws042'
Get-Service -Name CcmExec -ComputerName $computers |
    Select-Object MachineName, Status
```

Quickly surfaces which machines in a suspected-problem batch have a stopped or missing client service.

### Checking client log files directly for detailed diagnosis

```powershell
Invoke-Command -ComputerName 'ws042' -ScriptBlock {
    Get-Content "$env:WinDir\CCM\Logs\ClientIDManagerStartup.log" -Tail 50
}
```

SCCM client logs (particularly `ClientIDManagerStartup.log`, `PolicyAgent.log`, and `CcmExec.log`) contain detailed diagnostic information beyond what a simple service check reveals — useful when CCMEval alone doesn't resolve the issue.

---

## ⚠️ Common Mistakes

- Assuming a machine missing from SCCM reporting is definitely offline or decommissioned, without first checking whether it's actually online with a simply broken client.
- Not running CCMEval before attempting a manual client reinstall — CCMEval often resolves the same underlying issues automatically, without the more disruptive step of a full client uninstall/reinstall.
- Restarting the `CcmExec` service without first checking whether a deeper corruption (missing WMI namespace, broken certificate) is the actual cause — a simple restart won't fix issues CCMEval is specifically designed to detect and repair.
- Overlooking that CCMEval itself can take several minutes to complete its full evaluation and repair cycle — checking results too soon can look like a failure when it's simply still running.

---

## 📖 Further Reading

- Microsoft Learn: "About client health in Configuration Manager"
- Microsoft Learn: "CcmEval.exe reference"

---

## 💡 Wisdom from Mímir

Always check client health before investigating deployment or policy configuration further up the chain — a broken courier explains a missing delivery far more often than a mistake in the delivery instructions, and it's the faster thing to rule out first.

---

## 🔗 Related Notes

- [[Process and Service Cmdlets]]
- [[Triggering a Machine Policy Retrieval and Evaluation Cycle]]
- [[Restarting a Hung Windows Service]]
- [[Finding a Device's SCCM Collection Membership]]
