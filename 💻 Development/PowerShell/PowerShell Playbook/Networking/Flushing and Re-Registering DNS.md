---
type: powershell-pattern
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Beginner
tags:
  - networking
  - dns
  - troubleshooting
aliases:
  - Clear-DnsClientCache
  - ipconfig flushdns PowerShell
publish: true
permalink: powershell/flushing-and-re-registering-dns
---

# <span class="rune">ᚲ</span> Flushing and Re-Registering DNS

> *A flushed DNS cache only clears what THIS machine remembers — it does nothing to whatever a DNS server three hops away is still confidently handing out.*

---

## 🎯 Problem

A website or internal resource resolves to the wrong IP address, or won't resolve at all, even though DNS records were recently updated. Clear the local resolver cache and confirm the machine picks up the new record.

More generally, this pattern answers:
> How do I clear a machine's local DNS cache and re-register its own record with DNS, and how do I confirm whether stale DNS is actually the cause?

---

## 🤔 Mental Model

Picture the local DNS cache as sticky notes on this one computer's desk, each one a shortcut ("contoso.com = 10.0.0.5") written down after the last lookup so it doesn't have to ask again right away. Flushing the cache tears up those sticky notes — but it does nothing about the actual DNS server's own records, or any *other* machine's sticky notes, or an ISP's resolver cache somewhere upstream. It only ever fixes staleness that lives on this one desk.

---

## 🧠 Why This Pattern Works

`Clear-DnsClientCache` is the PowerShell-native equivalent of `ipconfig /flushdns`, purging every cached resolution the local DNS client service is holding — forcing the next lookup for each name to go back out to an actual DNS server rather than reusing a potentially stale local answer. `Register-DnsClient` (equivalent to `ipconfig /registerdns`) separately tells the machine to re-submit its own hostname-to-IP mapping to DNS, which matters when the machine's own record is what's wrong or missing, distinct from it having cached someone *else's* stale record — both mechanisms are covered generally in [[Networking Cmdlets]].

---

## 💻 PowerShell Solution

```powershell
# Clear the local DNS resolver cache
Clear-DnsClientCache

# Re-register this machine's own hostname with DNS
Register-DnsClient

# Confirm what a fresh lookup now returns
Resolve-DnsName -Name 'contoso.com'

# Compare against a specific, known-authoritative DNS server directly,
# bypassing the local cache and any other resolver in between
Resolve-DnsName -Name 'contoso.com' -Server '10.0.0.10'
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Clear the local cache

```powershell
Clear-DnsClientCache
```

Removes every cached DNS entry on this machine — no output on success, meaning "no news is good news" here.

### Step 2 — Re-register this machine's own record (if the issue is this machine's own DNS entry)

```powershell
Register-DnsClient
```

Only relevant when the problem is *this computer's* record being wrong or missing in DNS — not needed for troubleshooting resolution of some *other* hostname.

### Step 3 — Confirm resolution against the real DNS server directly

```powershell
Resolve-DnsName -Name 'contoso.com' -Server '10.0.0.10'
```

Querying a specific, known-authoritative server directly rules out any caching layer — local, upstream resolver, or otherwise — as the source of a stale answer, isolating whether the problem is truly the DNS record itself.

---

## 🚀 Common Use Cases

- Resolving "website shows the old server" complaints shortly after a DNS record change was made
- Confirming a DNS record update has actually propagated before escalating a "DNS isn't updating" ticket
- Fixing a machine whose own DNS registration became stale after a network change (new IP, VLAN move)
- Ruling local caching in or out as the cause before investigating the authoritative DNS server itself

---

## ⚖️ Alternatives

### Checking what's currently cached before flushing (diagnostic first, action second)

```powershell
Get-DnsClientCache | Where-Object Entry -like '*contoso*'
```

Useful to confirm a stale entry actually exists in the local cache before assuming that's the cause — sometimes the "stale" record is actually correct locally, and the real problem is upstream.

### Legacy `ipconfig` equivalents (for reference/compatibility)

```powershell
ipconfig /flushdns
ipconfig /registerdns
```

Functionally equivalent to the PowerShell cmdlets, still widely used out of habit — the cmdlet forms integrate more naturally into a scripted remediation.

---

## ⚠️ Common Mistakes

- Flushing the local cache and declaring the issue resolved, when the actual stale record lives on an upstream ISP resolver or a caching DNS server elsewhere in the path — entirely outside this machine's control.
- Confusing `Register-DnsClient` (this machine registering *its own* record) with a fix for a *different* hostname resolving incorrectly — it only affects this computer's own DNS entry.
- Not comparing against a known-authoritative DNS server directly (`Resolve-DnsName -Server`), leaving ambiguity about whether the problem is truly the record or just another caching layer in between.
- Assuming a DNS change is instant — TTL values on existing records mean caches (local and upstream) can legitimately continue serving the old answer until their TTL expires, independent of any local flush.

---

## 📖 Further Reading

- `Get-Help Resolve-DnsName -Full`
- `Get-Help about_DNS_Client`

---

## 💡 Wisdom from Mímir

Before declaring a DNS flush the fix, query the authoritative server directly with `Resolve-DnsName -Server`. If the correct answer comes back there but the client still shows the old one after a flush, the staleness lives somewhere between the two — not on the machine you just cleared.

---

## 🔗 Related Notes

- [[Networking Cmdlets]]
- [[Testing Connectivity to a Remote Host and Port]]
- [[Finding a Computer's IP Configuration and Adapter Details]]
- [[Tracing a Network Path Hop by Hop]]
