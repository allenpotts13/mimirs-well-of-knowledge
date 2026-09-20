---
type: concept
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Intermediate
tags:
  - powershell-fundamentals
  - remoting
  - winrm
aliases:
  - Invoke-Command
  - PSRemoting
  - WinRM
publish: true
permalink: powershell/remoting
---

# <span class="rune">ᛟ</span> Remoting

> *Remoting isn't "run this command and pull its text output back" — it's "send this whole script block to another machine, and let it execute there, on its own resources, as though you'd walked up to its console."*

---

## 🎯 Purpose

PowerShell Remoting lets you run commands on one or many remote computers as if you were sitting at each one — built on WS-Management (WinRM) rather than remote-desktop-style screen sharing. This is the foundation of essentially all real-world, fleet-scale troubleshooting and administration: rebooting a remote server, running a diagnostic against fifty machines at once, or pulling event logs from a computer you'll never physically touch all depend on remoting.

Every "remote computer" scenario throughout this Codex's Playbook — checking a service, pulling hardware info, restarting a hung process — relies on the mechanics covered here.

---

## 🧠 Key Ideas

- `Invoke-Command -ComputerName <name> -ScriptBlock { ... }` runs a script block on one or more remote machines and returns the results as objects, exactly like any other PowerShell output.
- `Enter-PSSession -ComputerName <name>` opens an **interactive** remote session — every command typed afterward runs on the remote machine until `Exit-PSSession`.
- `New-PSSession` creates a **persistent, reusable** connection — useful for running multiple separate commands against the same remote machine without reconnecting each time, and required for things like remote module state to persist between calls.
- Remoting requires **WinRM** to be enabled on the target (`Enable-PSRemoting` sets this up) and the caller to have appropriate permissions — it isn't available by default on an unconfigured machine.
- `Invoke-Command -ComputerName` accepts **multiple** computer names at once, running the same script block in parallel across all of them and returning results tagged with a `PSComputerName` property showing which machine each result came from.

---

## ⚙️ How It Works

When `Invoke-Command` targets a remote computer, PowerShell doesn't execute anything locally and just fetch a text result — it serializes the script block, sends it over WinRM to the target machine, executes it there using the target's own PowerShell engine and resources, and serializes the results back. This means the code genuinely runs *on the remote machine*: local files referenced inside the script block must exist on the remote machine, not the machine running the command.

Because results travel back over the network, they're **deserialized** objects — they retain their properties and data, but lose live methods (a deserialized `Process` object can't have `.Kill()` called on it directly the way a locally-retrieved one could). This distinction matters when a remoting-based script behaves differently than the exact same logic run locally.

```text
Invoke-Command -ComputerName srv01 -ScriptBlock { Get-Process }
        ↓ (script block SERIALIZED and sent over WinRM)
   srv01 executes Get-Process locally, using ITS OWN resources
        ↓ (results SERIALIZED and sent back)
   local session receives DESERIALIZED Process objects (data intact, live methods gone)
```

---

## 💻 Examples

```powershell
# One-off remote command
Invoke-Command -ComputerName 'srv01' -ScriptBlock { Get-Service -Name 'Spooler' }

# Multiple computers at once — results tagged with PSComputerName
Invoke-Command -ComputerName 'srv01','srv02','srv03' -ScriptBlock {
    Get-Service -Name 'Spooler'
} | Select-Object PSComputerName, Status

# Interactive remote session
Enter-PSSession -ComputerName 'srv01'
# (now every command runs on srv01 until:)
Exit-PSSession

# Persistent session reused across multiple calls
$session = New-PSSession -ComputerName 'srv01'
Invoke-Command -Session $session -ScriptBlock { Get-Process }
Invoke-Command -Session $session -ScriptBlock { Get-Service }
Remove-PSSession $session

# Passing local variables into a remote script block
$serviceName = 'Spooler'
Invoke-Command -ComputerName 'srv01' -ScriptBlock {
    param($svc)
    Get-Service -Name $svc
} -ArgumentList $serviceName
```

---

## 🚀 Real World Applications

- Running the same diagnostic (disk space, service status, event log check) across an entire list of servers in one command
- Restarting a hung service or process on a computer without needing RDP or physical access
- Using persistent sessions to run several related diagnostic commands against a problem server without repeatedly reconnecting
- Bulk-applying a configuration change validated on one machine to an entire fleet via `-ComputerName` with an array

---

## ⚖️ Advantages

- Scales identically from one computer to hundreds — the same script block just gets a longer `-ComputerName` list.
- Runs using the remote machine's own CPU/memory/local file access, not the caller's — appropriate for work that genuinely needs to happen "there."
- WinRM is a standard, firewall-manageable protocol, more consistent to secure and audit than ad-hoc remote execution tools.
- Persistent sessions (`New-PSSession`) avoid repeated authentication/connection overhead for multi-step remote work.

---

## ⚠️ Limitations

- Requires WinRM enabled and reachable on the target — not on by default on a fresh Windows install, and often blocked across untrusted network segments.
- Deserialized objects returned from remote sessions lose live methods, which can break code written assuming a locally-retrieved object.
- Double-hop authentication (a remote session trying to authenticate onward to a *third* machine, e.g., a file share) fails by default without CredSSP or Kerberos delegation configured — a frequent, confusing failure mode.
- Local variables aren't automatically visible inside a remote script block — they must be passed explicitly via `-ArgumentList` or the `$using:` scope modifier.

---

## 🚨 Common Mistakes

- Assuming a local variable is visible inside a remote `-ScriptBlock` without either `-ArgumentList` or `$using:variableName` — it silently evaluates as `$null` on the remote side otherwise.
- Calling a live method (like `.Kill()`) on an object returned from `Invoke-Command`, not realizing it's a deserialized copy without that functionality.
- Hitting a "double hop" authentication failure when a remote session tries to access a network resource on a third machine, and not recognizing it as a delegation problem rather than a permissions problem.
- Using `Enter-PSSession` for something that should really be a scripted `Invoke-Command` call — interactive sessions don't scale to multiple machines and aren't suited for automation.

---

## 📖 Further Reading

- `Get-Help about_Remote`
- `Get-Help Invoke-Command -Full`
- `Get-Help about_Remote_Variables` (for `$using:`)
- Microsoft Learn: "PowerShell Remoting security considerations"

---

## 💡 Wisdom from Mímir

If a remote script block behaves as though a variable is empty, check for the double-hop/`$using:` scope problem before suspecting the remote machine itself — remoting doesn't share local scope by default, and that surprise catches nearly everyone at least once.

---

## 🔗 Related Notes

- [[CIM and WMI]]
- [[Error Handling]]
- [[PowerShell Security Fundamentals]]
- [[PowerShell Codex]]
