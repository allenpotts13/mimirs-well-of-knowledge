---
type: powershell-pattern
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Beginner
tags:
  - windows
  - services
  - troubleshooting
aliases:
  - Restart-Service
  - Print Spooler Restart
publish: true
permalink: powershell/restarting-a-hung-windows-service
---

# <span class="rune">ᚲ</span> Restarting a Hung Windows Service

> *A service that "won't stop" is usually being asked the wrong question — the Service Control Manager and the underlying process are two different things, and only one of them might actually be stuck.*

---

## 🎯 Problem

The Print Spooler (or another common service) has stopped functioning correctly, but hasn't outright crashed — it's just stuck. Restart it cleanly, and know what to do if a graceful restart doesn't actually clear it.

More generally, this pattern answers:
> How do I restart a hung Windows service, and what's the escalation path if a normal restart doesn't resolve it?

---

## 🤔 Mental Model

A service has two layers that can each get stuck independently: the Service Control Manager's *record* of the service's state, and the actual running *process* behind it. `Restart-Service` asks the SCM layer to do a clean stop-then-start — which works when the SCM can still communicate with the process. But if the process itself is truly hung and unresponsive even to the SCM's own stop signal, the fix has to drop down a level and deal with the actual process directly, exactly as distinguished in [[Process and Service Cmdlets]].

---

## 🧠 Why This Pattern Works

`Restart-Service` issues a stop command through the SCM, waits for the service to report stopped, then issues a start command — a clean, ordered cycle that respects the service's own shutdown logic when that logic is still capable of responding. When it isn't — the service hangs indefinitely in a "Stopping" state — the underlying process needs to be identified and force-terminated directly, after which the SCM can usually start a fresh instance normally, since it no longer has a zombie process attached to the old service record.

---

## 💻 PowerShell Solution

```powershell
# Standard clean restart
Restart-Service -Name 'Spooler' -Force

# Check dependencies before restarting a service others rely on
Get-Service -Name 'Spooler' -DependentServices

# If Restart-Service hangs or fails — escalate to the underlying process
$service = Get-CimInstance -ClassName Win32_Service -Filter "Name='Spooler'"
$process = Get-Process -Id $service.ProcessId
Stop-Process -Id $process.Id -Force

# Now retry the service start cleanly
Start-Service -Name 'Spooler'
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Attempt the standard restart first

```powershell
Restart-Service -Name 'Spooler' -Force
```

`-Force` also stops any dependent services automatically as part of the cycle — the lightest-touch fix, and the correct first attempt for the vast majority of hung services.

### Step 2 — If it hangs, find the underlying process

```powershell
$service = Get-CimInstance -ClassName Win32_Service -Filter "Name='Spooler'"
$process = Get-Process -Id $service.ProcessId
```

`Win32_Service` exposes the `ProcessId` backing a given service — the bridge between the SCM's view (`Get-Service`) and the OS process view (`Get-Process`) covered in [[Process and Service Cmdlets]].

### Step 3 — Force-terminate the process, then restart the service cleanly

```powershell
Stop-Process -Id $process.Id -Force
Start-Service -Name 'Spooler'
```

Once the zombie process is actually gone, the SCM is free to start a genuinely fresh instance — this two-step escalation resolves the (fairly common) case where `Restart-Service` alone silently hangs.

---

## 🚀 Common Use Cases

- Restarting the Print Spooler after it stops accepting new print jobs (a very common real-world scenario)
- Recovering a service stuck in "Stopping" state that a normal restart attempt won't clear
- Scripted, unattended service health remediation as part of a scheduled monitoring/self-healing task
- Restarting a service and its dependents together in the correct order without manually managing each one

---

## ⚖️ Alternatives

### Remote restart via remoting

```powershell
Invoke-Command -ComputerName 'srv01' -ScriptBlock {
    Restart-Service -Name 'Spooler' -Force
}
```

Applies the same pattern remotely — useful for fleet-wide remediation of a known recurring service issue.

### Checking service recovery options (self-healing on future failures)

```powershell
sc.exe failure Spooler reset= 86400 actions= restart/60000/restart/60000/restart/60000
```

Configures Windows to automatically restart the service on future failures without manual intervention — `sc.exe` is used here because there's no direct PowerShell cmdlet equivalent for configuring service recovery actions as of Windows PowerShell 5.1/PowerShell 7.

---

## ⚠️ Common Mistakes

- Repeatedly retrying `Restart-Service` against a truly hung service, not realizing the underlying process itself (not just the SCM's request) is what's actually stuck.
- Force-killing a service's process without checking `-DependentServices` first, unexpectedly stopping other services that depended on it.
- Not confirming the service actually reached a healthy `Running` state afterward — `Start-Service` returning without error doesn't always guarantee the service is functioning correctly, just that it started.
- Assuming every hung service can be safely force-killed at the process level — some services (particularly security or system-critical ones) may leave the system in a degraded state if terminated abruptly rather than allowed to shut down properly.

---

## 💡 Wisdom from Mímir

When `Restart-Service` hangs instead of erroring outright, that itself is the diagnostic — it means the SCM is waiting on a process that isn't responding. Drop down to `Win32_Service.ProcessId` and deal with the process directly rather than waiting indefinitely or retrying the same command that already told you it was stuck.

---

## 🔗 Related Notes

- [[Process and Service Cmdlets]]
- [[Finding and Killing a Frozen Process]]
- [[CIM and WMI]]
- [[Checking SCCM Client Health on a Remote Computer]]
