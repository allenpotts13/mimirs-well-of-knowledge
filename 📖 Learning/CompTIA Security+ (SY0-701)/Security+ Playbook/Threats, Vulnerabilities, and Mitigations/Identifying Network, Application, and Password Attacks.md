---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Security+
difficulty: Intermediate
tags:
  - security-plus
  - threats-vulnerabilities
aliases:
  - Network and Application Attack Identification
publish: true
permalink: comptia-security-sy0-701/identifying-network-application-and-password-attacks
---

# <span class="rune">ᚲ</span> Identifying Network, Application, and Password Attacks

> *Password spraying avoids lockouts on purpose — that single design choice is the whole exam question.*

---

## 🎯 Problem

A scenario describes a network-level, application-level, or password-based attack. Identify the specific technique.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "a small request triggers a much larger response flooding the victim" | Amplified DDoS |
| "attack traffic is redirected using a spoofed source address" | Reflected DDoS |
| "domain name resolution is corrupted or redirected" | DNS poisoning/spoofing |
| "traffic between two parties is intercepted transparently" | On-path attack |
| "captured valid credentials are reused later to authenticate" | Credential replay |
| "user input isn't validated, allowing arbitrary commands/queries" | Injection |
| "input exceeds allocated memory, corrupting adjacent memory" | Buffer overflow |
| "a captured request is resent to repeat an action" | Replay attack |
| "a low-privilege account gains higher access than intended" | Privilege escalation |
| "an attacker accesses files outside the intended directory" | Directory traversal |
| "one weak password is tried across many accounts to avoid lockout" | Password spraying |
| "many passwords are tried against one account" | Brute force |
| "two different attacker actions accidentally produce the same hash" | Collision attack |
| "an attacker forces a connection to use a weaker, older protocol" | Downgrade attack |

---

## 🧠 Why This Pattern Works

Network attacks manipulate infrastructure-level trust (DNS, routing, traffic interception); application attacks exploit how software handles input or session state (injection, buffer overflow, replay, privilege escalation, directory traversal); password attacks exploit authentication directly, differing mainly by *strategy* (spraying trades thoroughness for stealth against one account, brute force trades stealth for thoroughness against a single target). Cryptographic attacks (downgrade, collision, birthday) specifically target weaknesses in the cryptographic scheme itself, not the application using it.

---

## 🚨 Common Mistakes

- Confusing password spraying (few passwords, many accounts, avoids lockout) with brute force (many passwords, one account, risks lockout) — the most commonly tested password-attack pair
- Mixing up amplified DDoS (response-size exploitation) with reflected DDoS (spoofed-source exploitation) as the same mechanism
- Treating a replay attack and credential replay as identical — replay can apply to any captured transaction, not just credentials specifically
- Missing that directory traversal is specifically about escaping the intended file path, not general unauthorized access

---

## 💡 Wisdom from Mímir

For password attacks specifically, ask "is this attacker trying to avoid lockouts by spreading thin, or accepting lockout risk to go deep on one account?" That question is the whole spraying-vs-brute-force distinction.

---

## 🔗 Related Notes

- [[Attack Indicators and Mitigation Techniques]] — full concept writeup
- [[Security+ Codex]]
