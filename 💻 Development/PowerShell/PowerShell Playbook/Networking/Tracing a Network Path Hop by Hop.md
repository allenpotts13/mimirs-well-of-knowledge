---
type: powershell-pattern
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Intermediate
tags:
  - networking
  - traceroute
  - latency
aliases:
  - Test-NetConnection TraceRoute
  - tracert PowerShell
publish: true
permalink: powershell/tracing-a-network-path-hop-by-hop
---

# <span class="rune">ᚲ</span> Tracing a Network Path Hop by Hop

> *"It's slow" and "it's unreachable" both look identical from the endpoints alone — a hop-by-hop trace is what turns either complaint into a specific router, a specific segment, a specific place to actually look.*

---

## 🎯 Problem

Users report intermittent slowness or connection failures reaching a specific remote resource. Determine at which point along the network path the delay or failure is actually occurring, rather than treating the whole connection as one opaque unknown.

More generally, this pattern answers:
> Which specific hop along the route to a remote host is introducing latency or dropping traffic?

---

## 🤔 Mental Model

Think of a network path as a chain of relay stations passing a message along. A simple ping only tells you whether the message eventually arrived and how long the whole relay took — a traceroute asks each relay station individually to acknowledge receipt, timestamping every handoff. That turns "the message was slow" into "the third relay station specifically added most of the delay" — a fundamentally more actionable finding.

---

## 🧠 Why This Pattern Works

`Test-NetConnection -TraceRoute` (and the classic `tracert`) work by sending packets with progressively increasing TTL (Time To Live) values — a packet with TTL=1 expires at the very first router, which sends back a response identifying itself; TTL=2 reaches and expires at the second router; and so on, building up the full path one hop at a time. Each hop's response time reveals exactly where along the path latency is being introduced, and a hop that stops responding entirely (shown as `* * *` in classic `tracert`, or timeouts in structured output) pinpoints where connectivity is actually breaking down.

---

## 💻 PowerShell Solution

```powershell
# Object-based traceroute via Test-NetConnection
Test-NetConnection -ComputerName 'contoso.com' -TraceRoute |
    Select-Object -ExpandProperty TraceRoute

# Classic tracert, for the familiar formatted hop-by-hop view with per-hop timing
tracert contoso.com

# Structured wrapper combining hop IP with a resolved hostname where possible
Test-NetConnection -ComputerName 'contoso.com' -TraceRoute |
    Select-Object -ExpandProperty TraceRoute |
    ForEach-Object {
        [pscustomobject]@{
            HopIP   = $_
            Hostname = (Resolve-DnsName -Name $_ -ErrorAction SilentlyContinue).NameHost
        }
    }
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Run the trace

```powershell
Test-NetConnection -ComputerName 'contoso.com' -TraceRoute
```

Returns a `TraceRoute` property containing an ordered array of every hop's IP address along the path.

### Step 2 — Cross-reference with `tracert` for per-hop timing

```powershell
tracert contoso.com
```

`Test-NetConnection -TraceRoute`'s `TraceRoute` property lists hop addresses but doesn't expose per-hop round-trip time as cleanly as classic `tracert`'s formatted output — for genuinely diagnosing *where* latency accumulates (not just the path itself), `tracert`'s three-timing-columns-per-hop output is often still the more immediately useful view.

### Step 3 — Identify the point of failure or delay

Look for either a hop where response times jump sharply (that specific router/link is adding latency) or a hop that stops responding entirely while later hops still do (a filtering device or misconfiguration at that specific point, not a fully broken path).

---

## 🚀 Common Use Cases

- Diagnosing intermittent slowness to a specific remote service by isolating which network segment introduces the delay
- Confirming whether a connectivity failure is happening at the local network, the ISP, or the destination's own network
- Investigating a sudden path change (a hop sequence that differs from a previously known-good trace) after an ISP or routing change
- Providing concrete hop-level evidence when escalating a network issue to an ISP or upstream provider

---

## ⚖️ Alternatives

### PowerShell 7+ / cross-platform `Test-Connection -TraceRoute`

```powershell
Test-Connection -TargetName 'contoso.com' -TraceRoute
```

PowerShell 7's redesigned `Test-Connection` includes native trace route support with more structured per-hop output than the Windows-only `Test-NetConnection`, useful in cross-platform scripts.

### Continuous path monitoring over time (rather than a single point-in-time trace)

```powershell
1..10 | ForEach-Object {
    Test-Connection -ComputerName 'contoso.com' -Count 1 |
        Select-Object @{N='Attempt';E={$_}}, ResponseTime
    Start-Sleep -Seconds 5
}
```

A single trace shows the path at one moment — for genuinely *intermittent* issues, repeated checks over time (or a dedicated continuous monitoring tool) are needed to catch the problem actually occurring.

---

## ⚠️ Common Mistakes

- Treating a single trace as conclusive for an intermittent problem — network paths and congestion can vary significantly between one trace and the next; several traces over time give a far more reliable picture.
- Assuming a hop that doesn't respond (`* * *`/timeout) is definitely broken — many routers are configured to deprioritize or silently drop the ICMP/UDP probe traffic traceroute uses, while still forwarding real application traffic normally.
- Interpreting the last responding hop as the actual point of failure without considering it might simply be a device configured not to respond to trace probes at all, even though the path continues normally beyond it.
- Running a trace once during a "slow" period and once during a "fine" period without controlling for time-of-day network congestion, then drawing a conclusion from what may just be normal variation.

---

## 📖 Further Reading

- `Get-Help Test-NetConnection -Full`
- `tracert /?` (external command help)

---

## 💡 Wisdom from Mímir

A hop that doesn't respond to a trace isn't automatically the problem — many routers deliberately ignore or deprioritize the exact probe traffic traceroute relies on while forwarding everything else perfectly normally. Treat a silent hop as a clue, not a verdict.

---

## 🔗 Related Notes

- [[Networking Cmdlets]]
- [[Testing Connectivity to a Remote Host and Port]]
- [[Finding a Computer's IP Configuration and Adapter Details]]
- [[Monitoring Real-Time Resource Usage]]
