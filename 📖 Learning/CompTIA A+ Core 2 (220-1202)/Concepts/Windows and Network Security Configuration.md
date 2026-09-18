---
type: concept
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA A+
difficulty: Intermediate
tags:
  - a-plus-1202
  - security
  - windows
aliases:
  - A+ Windows Security Settings
  - A+ SOHO Security
publish: true
permalink: comptia-a-core-2-220-1202/windows-and-network-security-configuration
---

# <span class="rune">ᛟ</span> Windows and Network Security Configuration

> *Every security feature here has an off switch — the exam tests whether you know when someone flipped it.*

---

## 🎯 Purpose

Covers 2.2, 2.3, 2.10, and 2.11 of the Security domain: Windows OS security settings, wireless security protocols/authentication, SOHO wireless/wired network security, and browser security configuration.

---

## 🧠 Key Ideas

- Windows security settings span authentication (login options, Hello, SSO), encryption (BitLocker, EFS), permissions (NTFS vs. share), and directory services (Active Directory)
- **NTFS permissions** apply locally regardless of access method; **share permissions** only apply over the network — when both apply, the *more restrictive* combination wins
- **WPA3** is the current wireless security standard; **WPA2** is still common; **TKIP** is a deprecated, weaker encryption method that shouldn't be chosen when AES is available
- SOHO router hardening (default password change, firmware updates, disabling UPnP, SSID/guest network management) is basic hygiene the exam expects as baseline knowledge
- Browser security settings (pop-up blockers, private browsing, certificate warnings, extensions from trusted sources only) are a user-facing security layer separate from OS/network security

---

## ⚙️ How It Works

### Windows OS security settings

| Area | Options |
|---|---|
| Login | Username/password, PIN, fingerprint, facial recognition, SSO, Windows Hello (passwordless) |
| Encryption | BitLocker (full disk), BitLocker-To-Go (removable media), EFS (per-file/folder) |
| Accounts | Local vs. Microsoft account; Standard, Administrator, Guest, Power User |
| UAC | Prompts for elevation before privileged actions |
| Permissions | NTFS (local + network) vs. share (network only) — most restrictive wins when combined |
| Active Directory | Domain join, login scripts, OU membership, Group Policy, security groups, folder redirection |

### Wireless security protocols

| Protocol | Status |
|---|---|
| WEP | Obsolete, insecure — should never appear as a correct answer |
| WPA2 (AES) | Still widely used, secure |
| WPA3 | Current standard, strongest |
| TKIP | Deprecated encryption, weaker than AES |

**Authentication:** RADIUS and TACACS+ (centralized AAA for network access), Kerberos (ticket-based, used by Active Directory), multifactor authentication.

### SOHO wireless/wired security

| Setting | Purpose |
|---|---|
| Change default password | Prevents using known default router credentials |
| Firmware updates | Patches known vulnerabilities |
| Disable UPnP | Prevents devices from automatically opening ports |
| Disable SSID broadcast | Hides network name from casual discovery (not a strong control alone) |
| Guest network | Isolates visitor devices from the main LAN |
| Firewall: disable unused ports, control port forwarding | Reduces attack surface |
| IP filtering, screened subnet | Restricts which devices/traffic are allowed |

### Browser security settings

Trusted sources for downloads/extensions, browser patching, pop-up blockers, clearing cache/browsing data, private browsing, valid-certificate secure connections, secure DNS, ad blockers, and proxy configuration.

---

## 💻 Examples

```text
"A user has NTFS 'Full Control' locally but only 'Read' via a network
share permission."
→ Effective access over the network is Read — most restrictive combination wins

"A SOHO router is still using its default administrator password."
→ First hardening step: change the default password

"A wireless network needs the strongest currently available encryption
standard."
→ WPA3

"A browser repeatedly shows certificate warnings for a site that should be
secure."
→ Do not proceed — likely an invalid certificate or an on-path attack

"A file needs to be encrypted individually without encrypting the entire
drive."
→ EFS (Encrypting File System), not BitLocker
"
```

---

## 🚨 Common Mistakes

- Assuming NTFS permissions apply over a network share the same way as locally — share permissions can further restrict access
- Choosing TKIP over AES when both are available — AES is stronger
- Treating "disable SSID broadcast" as a real security control rather than light obscurity — determined attackers can still find hidden SSIDs
- Confusing BitLocker (full-disk encryption) with EFS (file/folder-level encryption) — different scope, different use case

---

## 📖 Further Reading

- [CompTIA A+ Core 2 (220-1202) official exam objectives](https://www.comptia.org/certifications/a)

---

## 🔗 Related Notes

- [[Security Fundamentals, Malware, and Social Engineering]]
- [[Choosing the Right Wireless Security Protocol]] — drill note
- [[A+ Core 2 Codex]]
