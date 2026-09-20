---
type: concept
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Beginner
tags:
  - powershell-domain-reference
  - user-sessions
aliases:
  - quser
  - Get-LocalUser
publish: true
permalink: powershell/user-and-session-cmdlets
---

# <span class="rune">ᛟ</span> User and Session Cmdlets

> *"Who's actually logged into this machine right now, and how?" is a question PowerShell answers with three different tools depending on whether the account is local, domain, or an active logon session.*

---

## 🎯 Purpose

Troubleshooting login and session issues requires distinguishing between three related-but-different questions: who has a *local account* on this machine, who is *currently logged on* (interactively or via RDP), and what does a *domain account's* state look like. PowerShell answers each with a different tool — local accounts via `Get-LocalUser`, active sessions via the legacy `quser`/`query user` command (still the most reliable option for this specific question), and domain accounts via the [[Active Directory Cmdlets|AD module]].

---

## 🧠 Key Ideas

- `Get-LocalUser` and `Get-LocalGroup`/`Get-LocalGroupMember` manage the **local SAM** account database — independent of any domain account state.
- There is no fully native PowerShell cmdlet for "list active logon sessions" — `quser` (or `query user`) remains the standard tool, and its text output is commonly parsed or wrapped in a function for structured use.
- `query session` shows session IDs, states (Active/Disc), and session names — useful for identifying disconnected RDP sessions that are still holding resources.
- `logoff <sessionID>` (a classic console command, still usable from PowerShell) forcibly ends a specific session by ID — often needed to clear a stuck disconnected session.
- `Get-CimInstance -ClassName Win32_LoggedOnUser` and `Win32_LogonSession` expose logon session data through CIM as an alternative, fully object-based path, though the relationship between the two classes requires an associative query to interpret cleanly.

---

## ⚙️ How It Works

`Get-LocalUser` queries the local Security Accounts Manager (SAM) database directly and returns real objects — enabled/disabled state, last logon, password expiration — for accounts that exist locally on that specific machine, entirely separate from anything in Active Directory. This distinction matters because a "user not found" from `Get-LocalUser` says nothing about whether that user exists as a *domain* account, and vice versa.

Active session enumeration is one of the few areas where PowerShell still leans on a legacy text-based tool: `quser` predates PowerShell's object model and returns fixed-width text, which is why many troubleshooting scripts wrap it in a small parsing function to turn its columns into real objects for filtering.

```text
Get-LocalUser       → local SAM database accounts (this machine only)
quser / query user  → active/disconnected interactive & RDP sessions (text output)
Get-ADUser          → domain accounts (via AD module, separate concept entirely)
```

---

## 💻 Examples

```powershell
# Local accounts on this machine
Get-LocalUser

# Local group membership (e.g., who's a local admin)
Get-LocalGroupMember -Group 'Administrators'

# Who is currently logged on / disconnected (text output)
quser

# Same information, remote machine
quser /server:srv01

# Session list including session IDs (needed to force a logoff)
query session

# Force-logoff a specific disconnected session by ID
logoff 3

# CIM-based alternative — logged-on user association
Get-CimInstance -ClassName Win32_LoggedOnUser
```

---

## 🚀 Real World Applications

- Confirming whether a "locked computer" complaint is actually a disconnected RDP session still consuming resources
- Checking local administrator group membership when investigating unauthorized local privilege
- Force-logging-off a stuck or orphaned session that's blocking a file lock or preventing a clean profile reload
- Distinguishing a local-account password/login issue from a domain-account one before escalating to AD-specific troubleshooting

---

## ⚖️ Advantages

- `Get-LocalUser`/`Get-LocalGroupMember` give clean, object-based local account management without touching the registry or legacy `net user`/`net localgroup` commands.
- `quser`/`query session` remain fast, reliable, and universally available on every Windows version despite predating PowerShell.
- Combining local, session, and domain tools gives a complete picture of "who has access to this machine and how," from three independent angles.
- Forcing a logoff via session ID resolves a large share of "phantom" resource locks caused by disconnected-but-still-active sessions.

---

## ⚠️ Limitations

- `quser`/`query session` output is plain text — using it reliably in scripts requires either careful parsing or accepting its output as informational only.
- `Get-LocalUser` says nothing about domain accounts, and `Get-ADUser` says nothing about local accounts — conflating the two during an investigation wastes time.
- `Win32_LoggedOnUser`/`Win32_LogonSession` CIM classes require an associative query to be genuinely useful, which is noticeably more complex than the plain `quser` text output for the same basic question.
- Forcing a logoff via `logoff <ID>` ends the session immediately, without warning the user or saving unsaved work — a real risk if used carelessly.

---

## 🚨 Common Mistakes

- Running `Get-LocalUser` to check on a domain account and concluding "user doesn't exist," when the account is actually a domain account never visible to the local SAM database.
- Force-logging-off a session by ID without confirming first that it's actually the stale/disconnected one intended, risking loss of another user's active unsaved work.
- Assuming `quser` output columns are always in fixed positions across all Windows/locale configurations — text parsing scripts should be tested against the actual target OS version.
- Forgetting `quser /server:<name>` is needed to check sessions on a *remote* machine — running plain `quser` only reports the local machine's sessions.

---

## 📖 Further Reading

- `Get-Help Get-LocalUser -Full`
- `quser /?` (external command help, not a PowerShell cmdlet)
- Microsoft Learn: `Win32_LoggedOnUser` and `Win32_LogonSession` class references

---

## 💡 Wisdom from Mímir

Before assuming an account issue is a domain problem, check the local SAM database with `Get-LocalUser` first — a surprising number of "why can't this user log in" tickets turn out to involve a local account, not the domain account everyone assumed.

---

## 🔗 Related Notes

- [[Active Directory Cmdlets]]
- [[Checking a User's Current Logon Session]]
- [[Managing Local Administrator Group Membership]]
- [[PowerShell Codex]]
