---
type: concept
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Beginner
tags:
  - powershell-domain-reference
  - networking
aliases:
  - Test-NetConnection
  - Get-NetIPConfiguration
publish: true
permalink: powershell/networking-cmdlets
---

# <span class="rune">ᛟ</span> Networking Cmdlets

> *`ping` and `ipconfig` still work in PowerShell — but they return unstructured text. The NetTCPIP and NetAdapter modules return the same facts as real objects, ready to filter, script, and act on.*

---

## 🎯 Purpose

PowerShell's `NetTCPIP`, `NetAdapter`, and `DnsClient` modules (built into modern Windows) provide object-based replacements for classic command-line networking tools — `Test-NetConnection` instead of `ping`/`telnet`, `Get-NetIPConfiguration` instead of `ipconfig`, `Get-NetAdapter` instead of digging through Device Manager.

For troubleshooting, this matters because these cmdlets return structured properties (an actual `Boolean` for reachability, an actual `IPAddress` object) instead of text that has to be parsed — a script can act directly on the result instead of screen-scraping.

---

## 🧠 Key Ideas

- `Test-NetConnection -ComputerName <host> -Port <port>` tests both basic reachability (like `ping`) and, when `-Port` is specified, whether a specific TCP port is actually open — replacing the old habit of using `telnet` for port checks.
- `Get-NetIPConfiguration` returns a clean per-adapter summary (IP address, gateway, DNS servers) — the object-based equivalent of `ipconfig /all`, but far easier to filter or export.
- `Get-NetAdapter` lists network adapters with status, link speed, and interface index — `Restart-NetAdapter` and `Disable-NetAdapter`/`Enable-NetAdapter` act directly on the same objects.
- `Clear-DnsClientCache` and `Register-DnsClient` are the PowerShell-native equivalents of `ipconfig /flushdns` and `ipconfig /registerdns`.
- `Resolve-DnsName` replaces `nslookup` with structured, typed DNS query results (A, AAAA, MX, TXT records all as distinct object properties rather than raw text).

---

## ⚙️ How It Works

These cmdlets are thin, object-returning wrappers around the same underlying Windows networking stack that `ping`, `ipconfig`, and `nslookup` have always used — they aren't a separate mechanism, just a structured interface to it. `Test-NetConnection` in particular does more work than plain `ping`: with `-Port` specified, it performs an actual TCP handshake attempt against that port, reporting `TcpTestSucceeded` as a real boolean — directly answering "is the port open," not just "is the host alive."

```text
Test-NetConnection -ComputerName srv01 -Port 443
        ↓
  ICMP ping AND a TCP handshake attempt on port 443
        ↓
  returns object: PingSucceeded=True, TcpTestSucceeded=True/False
```

---

## 💻 Examples

```powershell
# Basic reachability, structured result
Test-NetConnection -ComputerName 'srv01'

# Reachability AND a specific port check (replaces telnet-based port tests)
Test-NetConnection -ComputerName 'srv01' -Port 443

# Full IP configuration, object-based
Get-NetIPConfiguration

# List adapters and their link status
Get-NetAdapter | Select-Object Name, Status, LinkSpeed

# Restart a specific adapter
Restart-NetAdapter -Name 'Ethernet'

# Flush and re-register DNS (PowerShell-native equivalents)
Clear-DnsClientCache
Register-DnsClient

# Modern nslookup replacement
Resolve-DnsName -Name 'contoso.com' -Type MX
```

---

## 🚀 Real World Applications

- Confirming whether a specific application port is reachable before assuming a firewall or service issue
- Pulling structured IP/gateway/DNS configuration for a machine during connectivity troubleshooting
- Restarting a stuck network adapter without navigating Device Manager manually
- Scripted DNS record checks (`Resolve-DnsName`) across multiple domains or record types

---

## ⚖️ Advantages

- `Test-NetConnection -Port` combines what used to require two separate tools (`ping` and `telnet`) into one structured object result.
- Object-based output means results can be filtered, exported, and logged without any text parsing.
- Cmdlets act on the same objects they report — `Get-NetAdapter` results feed directly into `Restart-NetAdapter` without translation.
- `Resolve-DnsName` supports every common record type cleanly, unlike `nslookup`'s inconsistent text formatting across record types.

---

## ⚠️ Limitations

- `Test-NetConnection` is noticeably slower than raw `ping` because it gathers more diagnostic detail per call by default — not ideal in tight loops over many hosts without tuning parameters like `-InformationLevel Quiet`.
- Some of these modules/cmdlets are Windows-specific and behave differently (or don't exist) on PowerShell running on Linux/macOS.
- `Test-NetConnection -Port` reports `TcpTestSucceeded=False` for both "port closed" and "host unreachable" scenarios — it doesn't always distinguish clearly why the connection failed.
- Adapter-related cmdlets (`Restart-NetAdapter`, `Disable-NetAdapter`) typically require an elevated session to actually change adapter state.

---

## 🚨 Common Mistakes

- Running `Test-NetConnection` in a loop over dozens of hosts without `-InformationLevel Quiet` or `-WarningAction SilentlyContinue`, producing excessive per-host diagnostic noise and slow execution.
- Interpreting `TcpTestSucceeded=False` as "the port is definitely closed" without considering the host might be entirely unreachable at the network layer instead.
- Forgetting `Clear-DnsClientCache` only clears the *local* resolver cache — it does nothing to any upstream DNS server's cache, which may still serve stale records.
- Assuming `Get-NetAdapter`/`Restart-NetAdapter` will work identically on every OS PowerShell runs on, rather than confirming these modules exist on the target platform first.

---

## 📖 Further Reading

- `Get-Help Test-NetConnection -Full`
- `Get-Command -Module NetTCPIP, NetAdapter, DnsClient`
- Microsoft Learn: "NetTCPIP module reference"

---

## 💡 Wisdom from Mímir

When a script needs to check many hosts quickly, add `-InformationLevel Quiet` to `Test-NetConnection` and treat the result as a boolean — the full diagnostic detail is valuable for one host at a time, but becomes noise (and a real time cost) at scale.

---

## 🔗 Related Notes

- [[Testing Connectivity to a Remote Host and Port]]
- [[Flushing and Re-Registering DNS]]
- [[Finding a Computer's IP Configuration and Adapter Details]]
- [[PowerShell Codex]]
