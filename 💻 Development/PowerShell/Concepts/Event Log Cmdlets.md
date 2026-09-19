---
type: concept
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Intermediate
tags:
  - powershell-domain-reference
  - event-logs
aliases:
  - Get-WinEvent
  - Get-EventLog
publish: true
permalink: powershell/event-log-cmdlets
---

# <span class="rune">ᛟ</span> Event Log Cmdlets

> *The Windows Event Log already recorded what happened before the ticket was ever opened — the skill is querying it precisely enough to find the one relevant entry among millions.*

---

## 🎯 Purpose

`Get-WinEvent` (modern) and `Get-EventLog` (legacy, deprecated in PowerShell 7+) query the Windows Event Log — the system's built-in record of application errors, security events, and system-level occurrences. For troubleshooting, the event log is frequently the fastest path to root cause: a crash, failed logon, or service failure almost always leaves a specific, timestamped, identifiable entry.

The skill here isn't just knowing the cmdlet — it's building efficient filters, because event logs can contain millions of entries and a naive query against them is painfully slow.

---

## 🧠 Key Ideas

- `Get-WinEvent` supports both classic logs (Application, System, Security) and the newer structured "Applications and Services" logs — `Get-EventLog` only supports classic logs and is unavailable in PowerShell 7+.
- `-FilterHashtable` is the efficient, server-side filtering mechanism for `Get-WinEvent` — filtering by `LogName`, `Id`, `Level`, and `StartTime` this way is dramatically faster than pulling everything and piping into `Where-Object`.
- Event `Level` values follow a standard numeric scale: 1=Critical, 2=Error, 3=Warning, 4=Information, 5=Verbose.
- `-MaxEvents` limits how many entries are returned — essential when just checking "the last N events" rather than an entire log's history.
- `Get-WinEvent -ListLog *` lists every available log by name — useful for discovering the exact log name needed (e.g., `Microsoft-Windows-PrintService/Operational` for print-specific issues) before building a targeted query.

---

## ⚙️ How It Works

`-FilterHashtable` translates its keys directly into the underlying XPath query the Windows Event Log service uses internally, meaning the filtering happens at the log provider itself rather than after PowerShell has already retrieved every entry. This is the difference between a query that returns in under a second and one that takes minutes on a busy server's Security log — `Get-WinEvent -LogName Security | Where-Object {$_.Id -eq 4625}` retrieves and deserializes every single security event before filtering, while `Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4625}` filters before anything is even pulled across.

```text
Get-WinEvent -FilterHashtable @{LogName='System'; Level=2; StartTime=(Get-Date).AddDays(-1)}
        ↓
  translated to an XPath query, executed by the log provider itself
        ↓
  ONLY matching entries (errors, last 24h) are returned — fast, even on huge logs
```

---

## 💻 Examples

```powershell
# Efficient filtered query — errors in System log, last 24 hours
Get-WinEvent -FilterHashtable @{
    LogName   = 'System'
    Level     = 2
    StartTime = (Get-Date).AddDays(-1)
}

# Specific event ID (failed logon = 4625)
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4625} -MaxEvents 20

# Discover available log names
Get-WinEvent -ListLog * | Where-Object {$_.RecordCount -gt 0} | Select-Object LogName

# Reading a specific "Applications and Services" log
Get-WinEvent -LogName 'Microsoft-Windows-PrintService/Operational' -MaxEvents 50

# Legacy cmdlet — Windows PowerShell 5.1 only
Get-EventLog -LogName Application -Newest 20 -EntryType Error

# Extracting the message text for review
Get-WinEvent -FilterHashtable @{LogName='System'; Id=6008} |
    Select-Object TimeCreated, Message
```

---

## 🚀 Real World Applications

- Pulling the last N system errors to correlate with a reported crash or reboot time
- Searching the Security log for failed logon events (ID 4625) during an account lockout investigation
- Checking application-specific operational logs (print, Group Policy, Windows Update) for service-specific failure detail
- Confirming an unexpected shutdown/restart via System log event IDs (6006/6008/1074/41)

---

## ⚖️ Advantages

- `-FilterHashtable` makes even huge, busy production logs queryable in seconds instead of minutes.
- Covers both classic and modern structured logs, unlike the legacy `Get-EventLog` which is limited to classic logs only.
- Returns full structured event objects, including raw XML event data when needed for deep forensic detail (`.ToXml()`).
- Event IDs are documented and consistent across Windows versions for the most common troubleshooting scenarios, making them reliable search anchors.

---

## ⚠️ Limitations

- `Get-EventLog` is absent entirely from PowerShell 7+ — scripts relying on it must be rewritten with `Get-WinEvent` for cross-version compatibility.
- `-FilterHashtable`'s XPath-based filtering has a learning curve — building efficient filters isn't as immediately intuitive as `Where-Object` syntax.
- Very old entries may have rolled off the log entirely depending on log size/retention settings, especially on defaults that cap by file size rather than duration.
- Remote querying (`-ComputerName`) requires appropriate permissions and, for the Security log specifically, often additional rights beyond standard remote access.

---

## 🚨 Common Mistakes

- Using `Get-WinEvent -LogName X | Where-Object {...}` instead of `-FilterHashtable`, and being surprised at how slow the query is against a large log.
- Forgetting `Level` values are numeric (2=Error, 3=Warning) and filtering with the wrong number, silently returning zero or wrong-severity results.
- Not checking `Get-WinEvent -ListLog *` first when troubleshooting an application-specific issue, missing a dedicated operational log that would have been far more targeted than the generic Application log.
- Assuming the classic Application/System/Security logs contain everything — many modern Windows components log exclusively to their own "Applications and Services" logs instead.

---

## 📖 Further Reading

- `Get-Help Get-WinEvent -Full`
- `Get-Help about_WinEvent` (filtering syntax reference)
- Microsoft Learn: "Windows Event Log reference" and common Security event IDs

---

## 💡 Wisdom from Mímir

Always reach for `-FilterHashtable` over `Where-Object` when querying event logs — the difference isn't stylistic, it's the difference between a query that returns instantly and one that visibly stalls on a busy production server.

---

## 🔗 Related Notes

- [[Reading the Last N System Errors from the Event Log]]
- [[Auditing Failed Login Attempts from the Security Log]]
- [[The Pipeline]]
- [[PowerShell Codex]]
