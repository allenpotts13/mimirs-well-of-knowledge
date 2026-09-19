---
type: concept
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Beginner
tags:
  - powershell-domain-reference
  - processes
  - services
aliases:
  - Get-Process
  - Get-Service
publish: true
permalink: powershell/process-and-service-cmdlets
---

# <span class="rune">ᛟ</span> Process and Service Cmdlets

> *A frozen process and a hung service look similar from the outside, but PowerShell treats them as genuinely different object types — knowing which one you're actually dealing with decides which cmdlet family solves the problem.*

---

## 🎯 Purpose

`Get-Process`/`Stop-Process`/`Start-Process` manage running processes, while `Get-Service`/`Restart-Service`/`Set-Service` manage Windows services — the persistent, often-no-UI programs that run in the background and are managed by the Service Control Manager (SCM). Both families follow identical `Get-`/`Stop-`/`Start-`/`Restart-` conventions, but they operate on fundamentally different underlying objects, with different lifecycles and different failure modes.

Troubleshooting "something isn't responding" almost always starts by figuring out which of these two categories the misbehaving thing actually belongs to.

---

## 🧠 Key Ideas

- A **process** is any running executable — `Get-Process` returns live `System.Diagnostics.Process` objects with CPU, memory, and handle-count properties.
- A **service** is a specific Windows Service Control Manager–registered background program — `Get-Service` returns `Status` (Running/Stopped/Paused) and `StartType` (Automatic/Manual/Disabled), not resource usage.
- `Stop-Process -Force` terminates a process immediately (equivalent to `taskkill /F`); it does not attempt any graceful shutdown.
- `Restart-Service` stops and starts a service in one call — safer and simpler than manually chaining `Stop-Service`/`Start-Service`, especially when dependent services are involved.
- Services can have **dependencies** — stopping a service other services depend on will fail (or cascade-stop them) unless `-Force` is used, and `Get-Service -RequiredServices`/`-DependentServices` reveals this relationship before acting.

---

## ⚙️ How It Works

Processes and services are managed through entirely different underlying subsystems: processes are tracked by the OS kernel's process table and can be inspected/killed directly via their `Id`; services are tracked and started/stopped through the Service Control Manager, an intermediary that also manages startup type, recovery actions (what happens if a service crashes), and dependencies between services. This is why a "hung" service sometimes needs `Restart-Service`, and other times its underlying process (visible in `Get-Process`) needs to be force-killed directly when the SCM itself can't get a response from it.

```text
Get-Service -Name Spooler          → SCM-level object: Status, StartType, dependencies
Get-Process -Name spoolsv          → OS-level object: CPU, memory, handles, the actual running executable

Restart-Service tries the SCM path first (graceful) 
  → if the underlying process is truly hung and unresponsive to SCM signals,
    Stop-Process -Force on the process itself may be the only way to clear it
```

---

## 💻 Examples

```powershell
# Process management
Get-Process -Name notepad
Stop-Process -Name notepad -Force
Start-Process -FilePath 'notepad.exe'

# Sort processes by resource usage
Get-Process | Sort-Object CPU -Descending | Select-Object -First 5

# Service management
Get-Service -Name 'Spooler'
Restart-Service -Name 'Spooler' -Force

# Checking dependencies before stopping a service
Get-Service -Name 'Spooler' -RequiredServices
Get-Service -Name 'Spooler' -DependentServices

# Changing a service's startup type
Set-Service -Name 'Spooler' -StartupType Automatic

# Finding the process behind an unresponsive service
Get-Process -Name spoolsv | Select-Object Id, Responding, CPU
```

---

## 🚀 Real World Applications

- Restarting the Print Spooler or another hung service without a full reboot
- Identifying and force-killing a frozen application process that's stopped responding
- Checking service dependencies before stopping something that other services rely on
- Changing a service's startup type to `Disabled` or `Automatic` as part of a hardening or troubleshooting step

---

## ⚖️ Advantages

- Clear Verb-Noun separation between process and service management avoids ambiguity about which layer a command affects.
- `Restart-Service` collapses a common two-step manual process into one safe, atomic call.
- Dependency inspection (`-RequiredServices`/`-DependentServices`) prevents surprise cascading failures from stopping a shared service.
- `-Force` on both `Stop-Process` and service cmdlets provides an explicit, deliberate escape hatch for genuinely stuck situations.

---

## ⚠️ Limitations

- `Stop-Process -Force` performs no graceful shutdown at all — any unsaved application state is lost immediately.
- A service can report `Status: Running` in the SCM while its underlying process is completely unresponsive — `Get-Service` alone won't reveal that; cross-referencing `Get-Process`/`.Responding` is needed.
- Stopping a service with active dependents without `-Force` fails outright rather than warning and proceeding — the check has to happen before, not after.
- Some services are protected and cannot be stopped or modified even with administrative rights, by design (security-critical Windows services).

---

## 🚨 Common Mistakes

- Force-killing a process behind a misbehaving service instead of trying `Restart-Service` first, when a graceful restart would have resolved it without losing any service-managed state.
- Assuming `Get-Service -Status Running` guarantees the service is actually healthy and responsive — status only reflects what the SCM believes, not real responsiveness.
- Stopping a shared/dependency service without checking `-DependentServices` first, causing an unexpected cascade of other services stopping too.
- Confusing a process's `Responding` property (whether its UI thread is responding to the OS) with actual application-level health — a process can report `Responding: True` while still functionally broken.

---

## 📖 Further Reading

- `Get-Help Get-Process -Full`
- `Get-Help Get-Service -Full`
- `Get-Help about_Service_Control_Manager` (via Microsoft Learn — no local about-topic)

---

## 💡 Wisdom from Mímir

When a service "won't stop," check whether the problem is actually the SCM-level control, or the underlying process itself refusing to respond — `Get-Service` and `Get-Process` are answering two different questions about the same underlying program, and the fix depends entirely on which one is actually stuck.

---

## 🔗 Related Notes

- [[Finding and Killing a Frozen Process]]
- [[Restarting a Hung Windows Service]]
- [[CIM and WMI]]
- [[PowerShell Codex]]
