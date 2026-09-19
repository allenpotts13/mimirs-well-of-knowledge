---
type: powershell-pattern
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Beginner
tags:
  - networking
  - network-adapter
  - troubleshooting
aliases:
  - Restart-NetAdapter
  - Disable Enable NIC PowerShell
publish: true
permalink: powershell/resetting-a-stuck-network-adapter
---

# <span class="rune">ᚲ</span> Resetting a Stuck Network Adapter

> *"Have you tried unplugging it and plugging it back in" has a scripted, remote-friendly equivalent — and knowing it means never needing physical access to a network cable to perform the same reset.*

---

## 🎯 Problem

A machine's network connection has gone stale — no IP address, or an IP that no longer matches the network it's on — and needs the classic "disable, then re-enable" reset without physically touching a cable or navigating Device Manager.

More generally, this pattern answers:
> How do I reset a network adapter and force it to acquire a fresh IP configuration, entirely from the command line?

---

## 🧠 Why This Pattern Works

`Restart-NetAdapter` performs the same disable/re-enable cycle as manually toggling a network adapter in Device Manager or physically unplugging and reconnecting a cable — it forces the network stack to fully tear down and rebuild the adapter's state, including releasing and re-requesting a DHCP lease. This resolves a specific, common category of problem: a stale or corrupted adapter state that a simple `ipconfig /renew`-equivalent alone doesn't fully clear, as introduced generally in [[Networking Cmdlets]].

---

## 💻 PowerShell Solution

```powershell
# Identify the adapter by name first
Get-NetAdapter

# Full reset — disable, then re-enable
Restart-NetAdapter -Name 'Ethernet'

# Release and renew the DHCP lease without a full adapter restart (lighter-weight first attempt)
ipconfig /release
ipconfig /renew

# PowerShell-native DHCP renewal equivalent
Get-NetAdapter -Name 'Ethernet' | Set-NetIPInterface -Dhcp Enabled
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Identify the correct adapter

```powershell
Get-NetAdapter
```

Confirms the exact adapter name (`Ethernet`, `Wi-Fi`, or a vendor-specific name) before acting — especially important on machines with multiple adapters where acting on the wrong one wastes time or disrupts an unrelated connection.

### Step 2 — Try the lighter-weight fix first

```powershell
ipconfig /release
ipconfig /renew
```

A DHCP release/renew alone resolves many stale-IP issues without the more disruptive full adapter restart — worth trying first since it's faster and less likely to briefly interrupt other network-dependent processes.

### Step 3 — Escalate to a full adapter restart if the lighter fix doesn't resolve it

```powershell
Restart-NetAdapter -Name 'Ethernet'
```

Fully disables and re-enables the adapter, clearing more deeply stuck states than a DHCP renewal alone can fix.

---

## 🚀 Common Use Cases

- Resolving a stale or incorrect IP configuration without physical access to the machine
- Clearing a network adapter that's shown as "connected" but isn't actually passing traffic
- Remote remediation via [[Remoting]] for a machine reported as having intermittent connectivity
- Recovering an adapter after a driver update or Windows update leaves it in an inconsistent state

---

## ⚖️ Alternatives

### Remote reset via Invoke-Command

```powershell
Invoke-Command -ComputerName 'ws042' -ScriptBlock {
    Restart-NetAdapter -Name 'Ethernet'
}
```

Applies the same reset to a remote machine — worth noting that if the remoting session itself depends on the very adapter being restarted, the connection will briefly (and expectedly) drop during the reset.

### Full network stack reset (more aggressive, for deeper corruption)

```powershell
netsh winsock reset
netsh int ip reset
Restart-Computer
```

A more invasive reset of the entire Windows networking stack (Winsock catalog and TCP/IP stack), used when adapter-level resets alone don't resolve deeper corruption — requires a reboot to take effect and is a bigger intervention than a simple adapter restart.

---

## ⚠️ Common Mistakes

- Running `Restart-NetAdapter` on a machine's *only* active network path via a remote session over that same adapter — the session will drop mid-command, which is expected but can look like a failure if not anticipated.
- Resetting the wrong adapter on a multi-adapter machine without first confirming which one is actually active via [[Finding a Computer's IP Configuration and Adapter Details]].
- Jumping straight to a full `netsh winsock reset`/reboot for a problem that a simple DHCP release/renew or adapter restart would have resolved with far less disruption.
- Not verifying the fix actually worked afterward (`Get-NetIPConfiguration`) — assuming the reset succeeded without confirming a valid new IP configuration was actually obtained.

---

## 💡 Wisdom from Mímir

Reach for the smallest effective fix first: DHCP release/renew, then adapter restart, then full stack reset — each step is progressively more disruptive, and jumping straight to the most aggressive option wastes time and risk on problems the lightest touch would have solved.

---

## 🔗 Related Notes

- [[Networking Cmdlets]]
- [[Finding a Computer's IP Configuration and Adapter Details]]
- [[Remoting]]
- [[Flushing and Re-Registering DNS]]
