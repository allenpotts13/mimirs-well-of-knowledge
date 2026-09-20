---
type: concept
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Intermediate
tags:
  - powershell-domain-reference
  - security
  - execution-policy
aliases:
  - Execution Policy
  - PowerShell Credentials
publish: true
permalink: powershell/powershell-security-fundamentals
---

# <span class="rune">ᛟ</span> PowerShell Security Fundamentals

> *Execution policy is a seatbelt, not a lock — understanding what it actually protects against (and what it doesn't) prevents both a false sense of security and unnecessary friction fighting it.*

---

## 🎯 Purpose

PowerShell's security model centers on a few key mechanisms: **execution policy** (governs whether scripts can run at all), **credential handling** (`Get-Credential`, `PSCredential` objects, secure strings), and **secure remoting** authentication. Understanding these prevents two opposite mistakes: treating execution policy as real security it isn't, and mishandling credentials in ways that leak them in plain text.

---

## 🧠 Key Ideas

- **Execution Policy** (`Get-ExecutionPolicy`, `Set-ExecutionPolicy`) controls whether `.ps1` script files are allowed to run — common values are `Restricted` (no scripts), `RemoteSigned` (local scripts run freely, downloaded scripts need a digital signature), and `Unrestricted`.
- Execution policy is explicitly **not a security boundary** — Microsoft's own documentation states it's meant to prevent *accidental* script execution, not a determined attacker; it can be bypassed in several well-known ways (`-ExecutionPolicy Bypass`, loading script content into memory rather than running a file).
- `Get-Credential` prompts for and returns a `PSCredential` object, storing the password as a `SecureString` in memory rather than plain text — the correct way to handle credentials interactively.
- Plain-text passwords should never be hardcoded in scripts; when credentials must be stored non-interactively, `Export-CliXml`/`Import-CliXml` on a `PSCredential` encrypts it using Windows DPAPI, tied to the specific user and machine that created it.
- PowerShell Remoting authenticates over WinRM using the current user's Windows credentials (Kerberos/NTLM) by default — no separate remoting-specific password is typically needed unless crossing untrusted network boundaries (where HTTPS/certificate-based WinRM is used instead of the default HTTP transport).

---

## ⚙️ How It Works

Execution policy is checked only when PowerShell runs a `.ps1` file directly — it does not prevent commands typed interactively, script content executed via `Invoke-Expression`, or scripts explicitly launched with `-ExecutionPolicy Bypass`. This is precisely why Microsoft describes it as a safety rail against *accidental* execution rather than a security control against a *deliberate* attempt — anyone with the ability to run PowerShell at all has several straightforward ways around it.

`SecureString` and DPAPI-based credential export work by encrypting the sensitive value using a key tied to the current Windows user account and machine — this is why a `PSCredential` exported to XML on one machine cannot be decrypted after copying that same file to a different machine or under a different user account; the encryption key never leaves that specific user+machine pairing.

```text
Set-ExecutionPolicy Restricted
        ↓
.\script.ps1 blocked ✔
Get-Content .\script.ps1 | Invoke-Expression   → NOT blocked (bypasses the file-execution check entirely)
powershell.exe -ExecutionPolicy Bypass -File .\script.ps1  → also NOT blocked
```

---

## 💻 Examples

```powershell
# Check and set execution policy (commonly RemoteSigned for a workstation)
Get-ExecutionPolicy
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser

# Interactive credential prompt — never hardcode passwords
$cred = Get-Credential

# Using the credential with a remote command
Invoke-Command -ComputerName 'srv01' -Credential $cred -ScriptBlock { Get-Service }

# Exporting an encrypted credential for reuse in a scheduled/unattended script
# (only decryptable by the SAME user on the SAME machine)
$cred | Export-CliXml -Path 'C:\Secure\cred.xml'
$cred = Import-CliXml -Path 'C:\Secure\cred.xml'

# Checking a script's digital signature
Get-AuthenticodeSignature -FilePath .\deploy.ps1
```

---

## 🚀 Real World Applications

- Setting `RemoteSigned` on workstations to allow locally-authored automation while still requiring signatures on anything downloaded from the internet
- Storing an encrypted service-account credential via `Export-CliXml` for a scheduled task that must run unattended
- Using `Get-Credential` interactively during ad-hoc remote troubleshooting instead of ever typing a password directly into a script
- Verifying a script's Authenticode signature before running it in a security-conscious environment

---

## ⚖️ Advantages

- `SecureString`/DPAPI-based credential handling avoids plain-text passwords appearing in scripts, logs, or command history.
- Execution policy, while not a hard security boundary, does meaningfully prevent accidental execution of unsigned or unexpected scripts in normal day-to-day use.
- Remoting's default Kerberos/NTLM authentication requires no extra credential management for same-domain scenarios.
- `Get-AuthenticodeSignature` gives a straightforward way to verify script provenance before trusting and running it.

---

## ⚠️ Limitations

- Execution policy provides **no protection at all** against a deliberate attacker with any PowerShell access — it should never be relied on as an actual security control.
- DPAPI-encrypted credentials via `Export-CliXml` are tied to one user on one machine — not portable, and not suitable for sharing a credential across multiple systems or accounts.
- Default WinRM remoting uses HTTP (encrypted via Kerberos/NTLM at the message level, but not TLS) unless explicitly configured for HTTPS — a consideration for remoting across untrusted networks.
- `Get-Credential` still requires a human to type a password interactively — not usable directly in fully unattended, first-run automation.

---

## 🚨 Common Mistakes

- Believing `Set-ExecutionPolicy Restricted` meaningfully "locks down" a machine against malicious PowerShell use — it doesn't, and shouldn't be the basis of a security decision.
- Hardcoding plain-text passwords in scripts "temporarily" during testing and forgetting to remove them before the script is shared or committed anywhere.
- Copying an `Export-CliXml`-encrypted credential file to a different machine or user account expecting it to still decrypt — DPAPI ties it to the original pairing specifically.
- Assuming PowerShell Remoting traffic is automatically encrypted with TLS by default — the default HTTP-based WinRM transport relies on Kerberos/NTLM message-level security, not a full TLS channel, unless HTTPS is explicitly configured.

---

## 📖 Further Reading

- `Get-Help about_Execution_Policies`
- `Get-Help about_Signing`
- `Get-Help Export-CliXml -Full`
- Microsoft Learn: "PowerShell execution policies" (explicitly documents it as not a security boundary)

---

## 💡 Wisdom from Mímir

Never present execution policy as "the security" of a PowerShell environment when explaining it to someone else — it's a safety rail against accidents, and treating it as more than that leaves a real gap in whatever it was meant to protect.

---

## 🔗 Related Notes

- [[Remoting]]
- [[Error Handling]]
- [[Testing Credentials Without Locking an Account]]
- [[PowerShell Codex]]
