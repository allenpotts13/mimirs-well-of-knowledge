---
type: powershell-pattern
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Beginner
tags:
  - networking
  - ip-configuration
  - adapters
aliases:
  - Get-NetIPConfiguration
  - ipconfig PowerShell
publish: true
permalink: powershell/finding-a-computers-ip-configuration-and-adapter-details
---

# <span class="rune">ᚲ</span> Finding a Computer's IP Configuration and Adapter Details

> *`ipconfig /all` gives you the same facts as a wall of text to read carefully. `Get-NetIPConfiguration` gives you the same facts as an object you can filter, compare, and act on directly.*

---

## 🎯 Problem

A machine can't reach the network, or is getting an unexpected IP address. Pull its full IP configuration — address, gateway, DNS servers, adapter status — in a form that can be filtered, compared, or exported, not just read once and discarded.

More generally, this pattern answers:
> What is this machine's current IP configuration, and which network adapter is actually active and carrying traffic?

---

## 🤔 Mental Model

Think of `Get-NetIPConfiguration` as `ipconfig /all` translated into a structured form rather than a wall of text — the same underlying facts (address, subnet, gateway, DNS servers), but now something that can be piped, filtered, exported to CSV, or compared programmatically against a known-good baseline instead of being read once off a screen and then discarded.

---

## 🧠 Why This Pattern Works

`Get-NetIPConfiguration` returns one object per network adapter with structured, typed sub-properties (`IPv4Address`, `IPv4DefaultGateway`, `DNSServer`) rather than one giant block of formatted text — meaning a script can directly compare `$config.IPv4Address.IPAddress` against an expected value, or filter to only adapters where `NetAdapter.Status -eq 'Up'`, without any text parsing at all. This is the object-model advantage from [[Objects in PowerShell]] applied specifically to the networking domain covered in [[Networking Cmdlets]].

---

## 💻 PowerShell Solution

```powershell
# Full structured IP configuration for every adapter
Get-NetIPConfiguration

# Only the adapter that's actually up and has a default gateway (i.e., the "real" active connection)
Get-NetIPConfiguration | Where-Object {
    $_.NetAdapter.Status -eq 'Up' -and $_.IPv4DefaultGateway
}

# Clean summary across all adapters
Get-NetIPConfiguration | Select-Object InterfaceAlias,
    @{N='IPAddress';E={$_.IPv4Address.IPAddress}},
    @{N='Gateway';E={$_.IPv4DefaultGateway.NextHop}},
    @{N='DNSServers';E={$_.DNSServer.ServerAddresses -join ', '}}

# Adapter hardware/link details
Get-NetAdapter | Select-Object Name, Status, LinkSpeed, MacAddress
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Pull full configuration for every adapter

```powershell
Get-NetIPConfiguration
```

Returns one object per adapter, including disconnected/inactive ones — useful for a complete picture, but noisy if only the active connection matters.

### Step 2 — Isolate the actually-active adapter

```powershell
Where-Object { $_.NetAdapter.Status -eq 'Up' -and $_.IPv4DefaultGateway }
```

A machine with multiple adapters (Ethernet, Wi-Fi, VPN, virtual) can have several configured but only one actually carrying default traffic — filtering to "up, with a gateway" reliably identifies the real active connection.

### Step 3 — Reshape into a clean, readable summary

```powershell
Select-Object InterfaceAlias, @{N='IPAddress';E={$_.IPv4Address.IPAddress}}, ...
```

`Get-NetIPConfiguration`'s default display nests several properties as sub-objects (`IPv4Address`, `DNSServer`) — calculated properties flatten these into a single-row summary suitable for a report or export.

---

## 🚀 Common Use Cases

- Diagnosing "no internet access" by confirming whether the machine even has a valid IP/gateway/DNS configuration
- Identifying which of several network adapters (physical, VPN, virtual) is the one actually in use
- Building a fleet-wide report of IP configurations for network documentation or an IP conflict investigation
- Comparing a machine's current configuration against an expected static configuration after a network change

---

## ⚖️ Alternatives

### Legacy `ipconfig /all` (still works, plain text output)

```powershell
ipconfig /all
```

Faster to type for a quick manual look, but not suitable for filtering or scripting without parsing its text output.

### CIM-based approach for remote querying without PowerShell Remoting

```powershell
Get-CimInstance -ClassName Win32_NetworkAdapterConfiguration -Filter "IPEnabled=True" -ComputerName 'ws042' |
    Select-Object Description, IPAddress, DefaultIPGateway, DNSServerSearchOrder
```

An older CIM-based path to similar data, useful when `Get-NetIPConfiguration` itself isn't available remotely but basic CIM/WMI connectivity is.

---

## ⚠️ Common Mistakes

- Reading `Get-NetIPConfiguration`'s default output and missing that a machine has multiple adapters — the first one listed isn't necessarily the active one.
- Assuming every adapter with an IP address is actually in use — a disconnected VPN adapter or unused virtual switch can still show a stale or link-local configuration.
- Not checking `IPv4DefaultGateway` specifically when trying to identify the "real" internet-facing adapter among several configured ones.
- Forgetting that `DNSServer.ServerAddresses` is an array — treating it as a single string without joining it can produce confusing partial output in a report.

---

## 📖 Further Reading

- `Get-Help Get-NetIPConfiguration -Full`
- `Get-Help Get-NetAdapter -Full`

---

## 💡 Wisdom from Mímir

On a machine with multiple network adapters, always filter to `Status -eq 'Up' -and IPv4DefaultGateway` before trusting any single adapter's configuration — the "obviously correct" first adapter in the list is very often not the one actually carrying traffic.

---

## 🔗 Related Notes

- [[Networking Cmdlets]]
- [[Objects in PowerShell]]
- [[Resetting a Stuck Network Adapter]]
- [[Testing Connectivity to a Remote Host and Port]]
